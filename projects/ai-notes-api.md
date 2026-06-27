# Анализ проекта AI Notes API для роли ML Engineer

> Дата: 2026-06-27. Пометки `[уточнить]` — данные, требующие подтверждения.
> Буллеты ниже написаны по формуле X-Y-Z из `docs/resume-rules.md`
> (действие → измеримый результат → как/чем).

**Ключевой честный вывод:** Это **production-ориентированный async-бэкенд для LLM-приложения
(GenAI / Applied AI Engineering)**, а НЕ ML-проект в классическом смысле. В репозитории нет
обучения/дообучения моделей, датасетов, метрик качества, экспериментов, feature engineering,
MLOps-инфраструктуры (model registry, serving, мониторинг дрейфа). Проект **потребляет** хостед-модели
OpenAI через API (`src/ai_notes_api/llm/client.py`, `llm/embeddings.py`), а «ML-часть» — это
интеграция: agentic tool-calling, RAG поверх pgvector, генерация эмбеддингов, токенизация
(`tiktoken`), structured-output извлечение фактов. Корректное позиционирование под вакансию —
**ML Engineer (Applied / LLM / Platform) или AI Engineer / GenAI Backend**, но НЕ «ML Engineer
(Research / Modeling)». Самый серьёзный риск на собеседовании: если роль подразумевает математику,
обучение и оценку моделей — этот проект её не закрывает, и это вскроется на первом же вопросе
«а как вы измеряли качество модели / что обучали». Заявлять надо честно: «инженер LLM-систем»,
а modeling-компетенции подтверждать другим проектом.

---

## 0. Метрики проекта (числа из репозитория)

| Метрика | Значение | Источник |
| --- | --- | --- |
| Код приложения (LOC) | ~10 940 строк, 113 `.py`-файлов | `src/` |
| Код тестов (LOC) | ~14 505 строк, 43 файла | `tests/` |
| Тест-функций | **456** | `tests/**/test_*.py` |
| Покрытие (line / branch) | **89.5% / 83.1%** (порог `fail_under = 85`) | `coverage.xml`, `pyproject.toml` |
| API-эндпоинтов | 26 | `api/v1/*.py` |
| Доменных сервисов | 12 | `services/` |
| Репозиториев | 9 | `repositories/` |
| ORM-моделей | 9 | `db/models/` |
| Alembic-миграций | 21 | `alembic/versions/` |
| LLM-инструментов (tools) | 5 | `tools/builtins/` |
| Celery-задач | 3 (generation, memory, processing) | `workers/tasks/` |
| Классов исключений (модулей) | 10 | `exceptions/` |
| Коммитов | 385 (Conventional Commits) | `git log` |
| Размерность эмбеддингов | 1536 (`Vector(1536)`) | `db/models/document_chunk.py` |
| Чанкинг по умолчанию | chunk_size=1000, overlap=200 токенов | `ingestion/chunking.py` |
| Retrieval | top_k=5, cosine distance | `services/llm_context.py`, `repositories/document_chunk.py` |

> Что всё ещё `[уточнить]` (не измеримо из кода): латентность первого токена в стриме, размер
> Docker-образа, время сборки, throughput generation-jobs, наличие ANN-индекса на эмбеддингах.

---

## 1. Ценность для роли

### AI / LLM-инжиниринг (ядро ценности под роль)
- **Agentic tool-calling цикл с OpenAI Responses API.** В `services/llm_service.py:490-504`
  реализован полноценный цикл «модель → вызов инструмента → результат → повтор»: пока модель
  возвращает `tool_calls`, выполняются хендлеры и их вывод дописывается обратно в контекст
  (`_collect_tool_outputs`, `llm_service.py:423-457`). Инструменты — реестр с JSON-schema
  (`tools/registry.py`, `tools/factory.py`, `tools/builtins/search_notes.py`).
- **RAG поверх pgvector.** Вопрос эмбеддится (`llm/embeddings.py`), затем
  `repositories/document_chunk.py:vector_search_in_user_session` делает поиск по косинусной
  дистанции (`DocumentChunk.embedding.cosine_distance(...)`, `order_by(distance).limit(top_k)`).
  Промпт собирается в `rag/prompt_builder.py` с явной маркировкой контекста как trusted и
  инструкцией «не выдумывай, если ответа нет».
- **Пайплайн ingestion документов.** `ingestion/text_extractor.py` (MarkItDown, в thread-pool,
  чтобы не блокировать event loop) → `ingestion/chunking.py` (`TokenTextChunker` на `tiktoken`,
  fixed-window с overlap, sha256-хеш чанка) → эмбеддинги → запись чанков
  (`services/document_processing.py:process_document`).
- **Long-term memory для чатов.** LLM-извлечение структурированных фактов через JSON-Schema
  strict-output (`memory/extractor.py`, `FACTS_SCHEMA`, `temperature=0`) + rolling-суммаризация
  (`memory/summarizer.py`). Обновление памяти вынесено в фон (Celery `workers/tasks/memory.py`).
- **Inference-инфраструктура.** SSE-стриминг ответа (`llm/client.py:stream_response_events`,
  `sse-starlette`), асинхронные generation-jobs через Celery (`workers/tasks/generation.py`),
  учёт токенов (prompt/completion/total) из usage модели (`llm_service.py:402-416`).

### Backend / инженерная культура (смежно-ценно)
- Чистая слоистая архитектура: `api/ → services/ → repositories/ → db/models`, DI через
  FastAPI-зависимости (`api/v1/dependencies.py`).
- Полностью async-стек (FastAPI + SQLAlchemy 2.0 async + asyncpg), 21 Alembic-миграция,
  строгий mypy (`disallow_untyped_defs`, `disallow_any_unimported`), ruff с широким набором
  правил (включая `S` — security), 456 тест-функций в 43 файлах, line-coverage 89.5% /
  branch-coverage 83.1% (`coverage.xml`, `fail_under = 85`).

### Чего работодатель здесь НЕ увидит
- Обучения/дообучения/квантизации моделей, кастомных моделей, инференса собственных весов.
- Метрик качества (precision/recall, RAGAS, eval-датасеты) и A/B экспериментов.
- Feature engineering, работы с табличными данными, классического ML/DL.
- MLOps: experiment tracking, model registry, serving (Triton/vLLM/TorchServe), мониторинг
  дрейфа/качества в проде.

---

## 2. Backend-сильные стороны (честно)

| Область | Что есть в коде |
| --- | --- |
| Веб-фреймворк | FastAPI, версионирование `/api/v1`, роутеры по доменам (`api/v1/*.py`) |
| Async | Сквозной async I/O: SQLAlchemy async + asyncpg, async OpenAI-клиент, async Celery-таски через `asyncio.run` |
| БД | PostgreSQL + pgvector, ORM-модели с `Mapped[...]`, soft-delete/timestamp mixins (`db/models/datetime.py`), 21 миграция Alembic |
| Конкурентность | Атомарный «оптимистичный лок» генерации через conditional `UPDATE ... WHERE generation_status = IDLE` (`repositories/chat_session.py:acquire_generation_lock`) — защита от параллельных генераций в одной сессии |
| Очереди | Celery + Redis: generation, memory, processing (`workers/tasks/*`) |
| Хранилище | S3-совместимое (MinIO/`aioboto3`), presigned-URL (`storage/document_storage.py`) |
| Auth | JWT (`python-jose`), bcrypt-хеши (`core/security.py`), Bearer-схема |
| Качество | mypy strict, ruff (E,W,F,I,D,B,S,UP,SIM,TRY,PL,C90…), pytest, coverage, pre-commit, deptry, pip-audit, Conventional Commits |
| Деплой | Multi-stage Dockerfile (non-root user, HEALTHCHECK), `compose.yml` (api+worker+db+redis+minio), CI на GitHub Actions с pgvector/redis-сервисами |

### Чего нет (не приписывать)
- **Нет rate-limiting, кэширования ответов, идемпотентности на уровне API** — для AI-бэкенда
  с дорогими LLM-вызовами это заметный пробел; **самое критичное для собеседования**, если
  спросят про защиту от перерасхода токенов/стоимости.
- Нет горизонтального масштабирования/k8s-манифестов (Docker-user сделан «k8s-friendly», но
  самих манифестов нет).
- Нет observability: метрик (Prometheus), трейсинга, дашбордов — только логи (`loguru`).
- Нет retry/backoff и circuit-breaker вокруг внешних вызовов OpenAI/S3.
- Тесты есть и их много, но это юнит/интеграционные на SQLite (`aiosqlite`, `conftest.py`) —
  **vector_search на pgvector в тестах, скорее всего, не покрыт реальной БД** `[уточнить]`.

---

## 3. AI/ML-сильные стороны — честно

**Прямого ML (обучение/оценка моделей) в проекте НЕТ.** Честно заявлять можно следующее
LLM/AI-смежное:

- **RAG-пайплайн end-to-end**: chunking (`tiktoken`, overlap) → эмбеддинги → векторный поиск
  (cosine, pgvector) → сборка grounded-промпта с цитированием чанков. Это сильный, востребованный
  навык для «Applied/LLM ML Engineer».
- **Agentic LLM (function calling)**: реестр инструментов с JSON-schema, цикл исполнения,
  безопасная сериализация результатов (`tools/registry.py:call`).
- **Structured output / constrained decoding**: strict JSON-Schema при извлечении фактов
  (`memory/extractor.py`), `temperature=0` для детерминизма.
- **Токенизация и управление контекстом**: `tiktoken`, лимит контекстных сообщений
  (`LLM_CONTEXT_MESSAGES_LIMIT`), учёт usage-токенов.
- **Prompt-инъекция — базовая защита**: контекст и история явно помечаются как «данные, не
  инструкции» (`rag/prompt_builder.py`, `memory/extractor.py`). Хороший инженерный сигнал.

### Чего нет (перечислить прямо)
- Нет оценки качества RAG/ответов (нет eval-харнесса, нет ground-truth, нет метрик
  retrieval@k / faithfulness).
- Нет работы с эмбеддингами «руками» (нормализация, dimensionality, выбор индекса
  HNSW/IVFFlat — в миграциях включён pgvector, но ANN-индекс на `embedding` `[уточнить]`,
  поиск идёт по точной дистанции).
- Нет fine-tuning, дистилляции, локального инференса, GPU-кода.
- Нет ноутбуков, экспериментов, датасетов.

---

## 4. Инженерное мышление — что рассказывать

- **Точность vs. стоимость/скорость в RAG.** Осознанный `top_k=5` и лимит контекста; chunking
  с overlap (компромисс «целостность контекста vs. дублирование токенов»). Есть о чём говорить:
  как выбирался размер чанка/overlap `[уточнить, цифры в коде дефолтные 1000/200]`.
- **Латентность vs. UX.** Два режима генерации: синхронный SSE-стриминг для интерактивности и
  async Celery-job для длинных задач (`api/v1/completions.py`). Память обновляется фоном, чтобы
  не блокировать ответ пользователю (`llm_service.py:419`).
- **Корректность под конкурентностью.** Генерация в сессии защищена атомарным DB-локом
  (compare-and-set через `UPDATE ... WHERE status=IDLE`), а не блокировкой в приложении —
  переживает несколько воркеров. Это сильный, «взрослый» аргумент.
- **Безопасность vs. удобство.** Промпты явно разделяют trusted-context и user-input
  (анти-prompt-injection); ruff-правила `S`; non-root в Docker; presigned-URL с TTL.
- **Расширяемость.** LLM-клиент абстрагирован (`LLMClient`), инструменты — через реестр/фабрику,
  что позволяет добавлять tools и менять провайдера (OpenAI-совместимый base URL в конфиге).
- **Ownership.** 385 коммитов, Conventional Commits, полный quality-gate (`task check`:
  lint+typecheck+tests+build+audit+deptry), CI с реальными сервисами pgvector/redis.

---

## 5. Bullet points для резюме

1. Спроектировал RAG-пайплайн для grounded-ответов по загруженным документам: extraction
   (MarkItDown) → token-chunking с overlap (`tiktoken`) → эмбеддинги OpenAI → векторный поиск
   по косинусной дистанции в PostgreSQL/pgvector, сократив привязку ответа к документу до
   top-5 релевантных чанков с цитированием источников.
2. Реализовал agentic LLM-слой на OpenAI Responses API с function-calling: реестр инструментов
   на JSON-Schema и итеративный цикл «вызов→результат→дозапрос», дав модели безопасный доступ к
   CRUD-операциям над заметками пользователя.
3. Построил long-term memory для чатов через LLM-извлечение структурированных фактов
   (strict JSON-Schema, `temperature=0`) и rolling-суммаризацию, вынеся обновление в фоновые
   Celery-задачи, чтобы не увеличивать латентность ответа.
4. Покрыл систему 456 тестами при line-coverage 89.5% / branch-coverage 83.1% (порог 85%) и
   строгим mypy/ruff, настроив CI на GitHub Actions с реальными сервисами pgvector и Redis и
   quality-gate (lint+types+tests+audit) на ~10.9k строк кода приложения.
5. Обеспечил корректность параллельных генераций атомарным DB-локом
   (`UPDATE ... WHERE status=IDLE ... RETURNING`), исключив гонки между несколькими воркерами
   без блокировок на уровне приложения.
6. Реализовал два режима инференса — SSE-стриминг для интерактивного UX и асинхронные
   Celery-jobs для длинных запросов — с учётом prompt/completion/total токенов на ответ.
7. Упаковал сервис в multi-stage Docker-образ (non-root, healthcheck) и docker-compose-стенд
   (api+worker+PostgreSQL/pgvector+Redis+MinIO), сократив время локального старта до одной
   команды `task docker` `[уточнить: размер образа, время сборки]`.

---

## 6. Навыки для резюме

- **Python:** Python 3.13, async/await, asyncio, dataclasses, typing (строгий mypy), генераторы.
- **AI / LLM:** OpenAI API (Responses, Embeddings), function calling / tools, RAG, prompt
  engineering, structured output (JSON-Schema), `tiktoken`, anti-prompt-injection. *(GenAI-смежное;
  НЕ путать с modeling/training.)*
- **Бэкенд:** FastAPI, SSE, JWT-auth, слоистая архитектура, DI, Pydantic v2.
- **Данные / поиск:** векторный поиск (pgvector, cosine), эмбеддинги, чанкинг, ingestion-пайплайн.
- **БД:** PostgreSQL, SQLAlchemy 2.0 (async), Alembic-миграции, asyncpg.
- **Очереди / async-обработка:** Celery, Redis (broker/backend), фоновые задачи.
- **Хранилище:** S3-совместимое (MinIO), `aioboto3`, presigned-URL.
- **DevOps:** Docker (multi-stage), docker-compose, GitHub Actions CI, healthchecks, `uv`, Taskfile.
- **Тестирование / качество:** pytest, pytest-asyncio, coverage (~89%), ruff, mypy strict,
  pre-commit, pip-audit, deptry.

**НЕ подтверждается этим репозиторием (закрывать другими проектами):** обучение/оценка моделей,
PyTorch/TF/sklearn, MLOps (MLflow/W&B, model registry, serving), feature engineering, классический
ML, работа с GPU, eval-метрики RAG.

**Заявлять пока рано:** «production-grade ML-система», «MLOps» — нет мониторинга качества/дрейфа;
«масштабируемый» — нет нагрузочного тестирования и k8s `[уточнить]`.

---

## 7. ATS-ключевые слова

**Подтверждаются этим проектом:** Python, FastAPI, async, asyncio, RAG, Retrieval-Augmented
Generation, LLM, OpenAI API, function calling, embeddings, pgvector, vector search, semantic
search, PostgreSQL, SQLAlchemy, Alembic, Celery, Redis, tiktoken, prompt engineering, JSON Schema,
structured output, SSE, JWT, Docker, docker-compose, CI/CD, GitHub Actions, pytest, mypy, S3/MinIO,
clean architecture.

**Нужно подтверждать ДРУГИМИ проектами (под целевую роль ML Engineer):** PyTorch, TensorFlow,
scikit-learn, model training, fine-tuning, model evaluation, MLflow / Weights & Biases, feature
engineering, model serving (Triton / vLLM / TorchServe), MLOps, model monitoring / drift, pandas /
NumPy, Kubernetes, experiment tracking.

---

## 8. GitHub README — что улучшить

- Добавить раздел **«Architecture»** со схемой потока RAG (upload → extract → chunk → embed →
  pgvector → retrieve → LLM) — это главный продаваемый артефакт под роль.
- Явно перечислить **AI-возможности и их границы** (используются хостед-модели OpenAI; нет
  обучения) — честность считывается положительно.
- Указать **метрики**, которых сейчас нет: латентность стрима `[уточнить]`, размер Docker-образа
  `[уточнить]`, число тестов/coverage (89%) — последнее уже можно вынести в текст, не только в бейдж.
- Добавить пример **eval-харнесса** хотя бы на 10–20 вопросов к документу (даже простой
  retrieval@k) — мгновенно повышает «ML-вес» проекта.

## 9. Рассказ на собеседовании

- **Проблема:** нужен бэкенд для AI-ассистента над личными заметками и документами, который
  отвечает с опорой на загруженные файлы и помнит контекст пользователя.
- **Что сделал:** RAG-пайплайн (chunking→эмбеддинги→pgvector cosine-поиск), agentic
  tool-calling над заметками, long-term memory (факты + суммаризация), два режима инференса
  (SSE-стрим и Celery-jobs).
- **Технологии:** FastAPI (async), PostgreSQL+pgvector, OpenAI Responses/Embeddings, Celery+Redis,
  S3/MinIO, Docker.
- **Сложности:** гонки при параллельных генерациях в одной сессии; блокировка event loop
  синхронной экстракцией текста; отделение «данных» от «инструкций» для модели.
- **Решения:** атомарный DB-лок (compare-and-set), вынос CPU-bound экстракции/чанкинга в
  thread-pool (`run_in_executor`), явная разметка trusted-context в промптах, фоновое обновление
  памяти.
- **Что улучшил бы:** добавил бы eval-метрики RAG (retrieval@k, faithfulness), ANN-индекс
  (HNSW) на эмбеддинги, rate-limiting и retry/backoff вокруг OpenAI, observability (метрики/трейсинг).

## Главная рекомендация

Позиционируй проект как флагман **LLM/Applied-AI Engineering** (RAG + agentic + async-инфраструктура),
а core-ML (обучение/оценка моделей) закрывай отдельным проектом — связка «прикладная LLM-система +
modeling-проект» точно бьёт в вилку «ML Engineer». Усиление с максимальным эффектом за 1–2 недели:
добавить **eval-харнесс для RAG** (retrieval@k + faithfulness на небольшом ground-truth наборе) и
**ANN-индекс HNSW** на эмбеддинги — это превращает «бэкенд с векторным поиском» в «измеримую
ML-систему с осознанным выбором индекса», что и спрашивают на собеседовании ML Engineer.
