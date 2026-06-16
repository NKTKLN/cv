# Анализ проекта Code Kata для роли Junior AI Backend / ML Engineer

> Анализ репозитория с точки зрения карьерного позиционирования.
> Дата: 2026-06-12. Пометки `[уточнить]` — данные, требующие подтверждения.

## Ключевая оговорка по позиционированию

**Code Kata — это ML-учебный репозиторий, а не backend-сервис.** В нём нет API, базы данных, Docker, деплоя и асинхронности. Стратегия: продавать его как **доказательство глубины ML-понимания + инженерной культуры**, а backend-навыки закрывать другими проектами (они есть — указать их рядом в резюме).

---

## 1. Ценность для роли

Что реально показывает проект:

- **Глубина ML, а не «fit-predict»**: алгоритмы реализованы from scratch (gradient boosting с ручным вычислением градиентов, decision trees, KNN, линейная/логистическая регрессия, bagging, random forest, stacking) и сравнены с scikit-learn / XGBoost. По замерам, точность собственных реализаций практически идентична библиотечным (разница в тысячных долях метрики или меньше).
- **Понимание ANN/vector search**: четыре ноутбука по приближённому поиску соседей — k-d trees, LSH, HNSW, Annoy. Прямой мост к RAG и vector databases. **Самая недооценённая сильная сторона репозитория.**
- **Инженерная культура выше уровня Junior**: ruff (~16 групп правил), mypy strict, pre-commit hooks, Taskfile как quality gate (lint + pip-audit + deptry), conventional commits (commitizen), uv.
- **Практический результат**: Kaggle Spaceship Titanic — score **0.80500** (прогресс с 0.80406 виден в git-истории); Titanic — **0.78229**. Полный пайплайн: feature engineering → EDA → сравнение CatBoost/LightGBM/XGBoost → Optuna (TPESampler, 150 trials) → финальная модель на GPU.
- **Переиспользуемый код**: `machine_learning/utils/` — типизированные модули оценки и визуализации (ROC, PR-curve, confusion matrices, feature importance) с Google-style docstrings.

Чего работодатель здесь не увидит: REST API, БД, деплой, LLM-интеграции, прод. Это закрывается другими проектами.

## 2. Backend-сильные стороны (честно)

| Есть | Нет (в этом репо) |
|---|---|
| Структура проекта, разделение utils/notebooks | REST API / FastAPI |
| Тулинг: uv, ruff, mypy strict, pre-commit | Базы данных |
| Taskfile с quality gate | Авторизация, валидация запросов |
| Security audit (pip-audit), deptry | Docker, CI/CD pipeline-файлы |
| Полная типизация (NDArray-аннотации) | Асинхронность, очереди |
| Conventional commits, версионирование | Деплой |

Производственная дисциплина переносится в резюме напрямую: code quality tooling, static typing, dependency hygiene. Backend-стек подтверждается другими проектами.

## 3. AI/ML-сильные стороны

- **From-scratch реализации** с бенчмарком против sklearn/XGBoost: GBDT (classification + regression), random forest, bagging, stacking, decision trees, KNN, линейная и логистическая регрессия. Точность совпадает с библиотечными реализациями с точностью до тысячных.
- **Approximate Nearest Neighbors**: k-d trees, LSH, HNSW, Annoy — фундамент vector search / retrieval.
- **Hyperparameter tuning**: grid search, random search, Optuna (TPESampler, objective-класс, визуализация optimization history / param importances), scikit-optimize, hyperopt.
- **Метрики**: отдельные исследования метрик классификации и регрессии + собственные evaluation-модули.
- **Feature engineering**: групповые/семейные признаки из идентификаторов, разбор составных полей (Cabin), агрегаты трат, доменные стратегии заполнения пропусков, label encoding.
- **Production-стек табличного ML**: XGBoost, CatBoost, LightGBM.

Нет: LLM, RAG, CV, NLP, inference-сервиса. ANN-ноутбуки — лучшая зацепка для разговора про RAG на собеседовании.

## 4. Инженерное мышление

- **Методический выбор**: «реализовать from scratch → сравнить с библиотекой» — trade-off понимание vs скорость разработки; результат сравнения подтверждён замерами.
- **Trade-offs ANN**: exact KNN vs четыре приближённых метода — разговор о «точность vs latency vs память».
- **Поддерживаемость**: шаблоны ноутбуков, вынос кода в типизированные utils, единый стиль через ruff.
- **Ownership**: соло-проект от инфраструктуры до моделей; итеративное улучшение видно в git-истории (0.80406 → 0.80500).
- **Производительность**: GPU-обучение CatBoost, детерминированность через фиксированные seeds.

## 5. Bullet points для резюме

1. Реализовал с нуля на Python/NumPy ключевые ML-алгоритмы (gradient boosting, random forest, decision trees, линейная и логистическая регрессия, KNN, bagging, stacking) и подтвердил бенчмарками, что их точность практически идентична реализациям scikit-learn и XGBoost (расхождение в тысячных долях метрики).
2. Реализовал и сравнил 4 алгоритма приближённого поиска ближайших соседей (k-d trees, LSH, HNSW, Annoy), изучив trade-off между точностью, скоростью поиска и памятью — фундамент vector search для RAG-систем.
3. Построил end-to-end ML-пайплайны для Kaggle: Spaceship Titanic — score 0.805, Titanic — 0.782 (feature engineering, обработка пропусков, EDA, сравнение моделей, тюнинг).
4. Спроектировал feature engineering пайплайн: извлечение групповых и семейных признаков из идентификаторов, разбор составных полей, агрегаты трат и доменно-обоснованные стратегии заполнения пропусков.
5. Автоматизировал подбор гиперпараметров CatBoost с помощью Optuna (TPE sampler, 150 trials, кросс-валидация), улучшив итоговый score на лидерборде [уточнить: на сколько vs baseline].
6. Применил production-стек градиентного бустинга (XGBoost, CatBoost, LightGBM) с системным сравнением моделей по единым метрикам перед выбором финальной.
7. Разработал библиотеку переиспользуемых, полностью типизированных утилит оценки и визуализации моделей (ROC/PR-кривые, confusion matrices, feature importance) с Google-style docstrings.
8. Настроил инфраструктуру качества кода: ruff (16+ групп правил), mypy в strict-режиме, pre-commit hooks, security-аудит зависимостей (pip-audit) и контроль неиспользуемых пакетов (deptry).
9. Организовал воспроизводимое окружение через uv с lock-файлом и Taskfile-автоматизацию (единые команды для линтинга, аудита, тестов и релизов).
10. Внедрил Conventional Commits с автоматическим версионированием и changelog (commitizen).
11. Создал отдельные исследования метрик классификации и регрессии с реализацией и интерпретацией каждой метрики (precision/recall trade-off, ROC AUC, регрессионные ошибки).
12. Ускорил обучение финальной модели CatBoost переносом на GPU [уточнить: измерялось ли ускорение в числах].

## 6. Навыки для резюме

- **Python**: Python 3.13, типизация (mypy strict, NDArray annotations), NumPy, pandas
- **ML/AI libraries**: scikit-learn, XGBoost, CatBoost, LightGBM, Optuna, hyperopt, scikit-optimize
- **Data processing**: pandas, feature engineering, обработка пропусков, EDA
- **Visualization**: matplotlib, seaborn, plotly
- **Vector search / ANN**: HNSW, LSH, k-d trees, Annoy
- **Code quality / tooling**: ruff, mypy, pre-commit, pip-audit, deptry, pytest + coverage (настроены; тестов в репо пока нет)
- **Workflow**: uv, Taskfile, Git, Conventional Commits, commitizen, Jupyter
- **Backend / Docker / cloud**: этим репозиторием не подтверждаются — указывать на основе других проектов
- **Soft skills**: самостоятельность, систематичность, документирование, итеративное улучшение результата

## 7. ATS-ключевые слова

Подтверждаемые этим проектом: `Python`, `Machine Learning`, `scikit-learn`, `XGBoost`, `CatBoost`, `LightGBM`, `Optuna`, `hyperparameter tuning`, `cross-validation`, `feature engineering`, `data preprocessing`, `EDA`, `pandas`, `NumPy`, `gradient boosting`, `ensemble methods`, `classification`, `regression`, `model evaluation`, `ROC AUC`, `approximate nearest neighbors`, `HNSW`, `vector search`, `type hints`, `mypy`, `ruff`, `pre-commit`, `Conventional Commits`, `Kaggle`, `reproducibility`, `GPU training`.

Для AI Backend-вакансий дополнительно (подтверждать другими проектами): `FastAPI`, `Docker`, `PostgreSQL`, `REST API`, `LLM`, `RAG`, `embeddings`, `async`, `CI/CD`.

## 8. GitHub README — что улучшить

- **Короткое описание**: "Systematic ML practice: classic algorithms implemented from scratch and benchmarked against production libraries (scikit-learn, XGBoost), plus end-to-end Kaggle pipelines."
- **Problem statement**: не «учусь», а «большинство туториалов учат вызывать API библиотек; этот репозиторий — про понимание алгоритмов на уровне реализации».
- **Features**: from-scratch реализации со сравнением; 4 ANN-алгоритма; Optuna-тюнинг; типизированная utils-библиотека.
- **Results — главное, чего не хватает в README**: таблица «моя реализация vs sklearn» (метрики практически идентичны — это сильный факт, его надо показать в числах) + Kaggle scores: Spaceship Titanic 0.805, Titanic 0.782.
- **How to run**: добавить one-liner `task init`. Исправить несоответствия: README упоминает Poetry, проект на uv; в `audit`-таске остался `poetry run`.
- **Tech stack + Architecture**: добавить бейджи (Python 3.13, ruff, mypy, license) и ссылки на сильнейшие ноутбуки (GBDT from scratch, HNSW) из README.
- **Future improvements**: обернуть лучшую модель в FastAPI inference-сервис с Docker; unit-тесты на from-scratch реализации; GitHub Actions; MLflow.

## 9. Рассказ на собеседовании

- **Проблема**: «Хотел понимать ML глубже уровня `model.fit()`. Построил систему регулярной практики: каждый алгоритм реализую с нуля, потом сравниваю с библиотечной версией.»
- **Что сделал**: from-scratch реализации (бустинг, леса, стекинг, ANN-поиск) с подтверждённой бенчмарками точностью на уровне библиотек; полные Kaggle-пайплайны (0.805 и 0.782); инженерная инфраструктура — строгая типизация, линтеры, pre-commit, security-аудит.
- **Технологии**: Python 3.13, NumPy, pandas, scikit-learn, XGBoost/CatBoost/LightGBM, Optuna, uv, ruff, mypy.
- **Сложности** (выбрать реальные): корректное вычисление градиентов в бустинге для классификации (log-loss, сигмоида, начальный логит); какие признаки реально двигали Kaggle score; trade-offs ANN-алгоритмов.
- **Решения**: типизированные utils вместо копипасты между ноутбуками; CatBoost после системного сравнения трёх бустингов; Optuna с TPE вместо grid search из-за размерности пространства гиперпараметров.
- **Что улучшил бы**: FastAPI-сервис с Docker вокруг модели; unit-тесты на from-scratch реализации (pytest уже настроен); CI на GitHub Actions; эксперимент-трекинг (MLflow).
- **Сильный ход** для AI Backend-вакансий: на вопрос про RAG/vector DB — «я реализовал HNSW и LSH сам и могу объяснить, почему vector search быстрый и где он теряет точность».

## 10. Открытые вопросы (что ещё уточнить)

Закрыто:
- ~~Titanic score~~ → **0.78229**
- ~~Бенчмарки «моя реализация vs sklearn»~~ → **измерялись, точность практически идентична (разница в тысячных или меньше)** — добавить числа в README
- ~~Backend-проекты~~ → **есть другие проекты** — связать их с этим репозиторием в резюме/портфолио

Остаётся уточнить:
1. Место на лидерборде Kaggle для score 0.805; прирост от тюнинга/фичей vs baseline.
2. Планируются ли тесты (pytest/coverage настроены, тестов нет) — написать хотя бы на utils и from-scratch модели.
3. Замерялось ли время обучения «моя реализация vs sklearn» (не только точность).
4. Количественное сравнение recall/скорости HNSW vs LSH vs Annoy.
5. Ускорение CatBoost на GPU vs CPU в числах.

## Главная рекомендация

Репозиторий закрывает ML-половину роли и инженерную культуру. Backend-половину закрывают другие проекты — в резюме и портфолио связать их явно: «code-kata = глубина ML, проект X = backend/API/деплой». Опциональное усиление (1–2 недели): обернуть обученную CatBoost-модель из Spaceship Titanic в FastAPI inference-сервис (Pydantic-валидация, обработка ошибок, логирование, Dockerfile, GitHub Actions, latency-замеры в README) — связка станет полностью самодостаточной.
