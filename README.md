# 📄 Резюме

Этот репозиторий содержит исходные файлы и автоматизацию, необходимые для создания резюме с помощью [RenderCV](https://github.com/rendercv/rendercv). Содержимое и дизайн резюме хранятся в YAML-файлах, а итоговые PDF- и Typst-файлы генерируются в директорию `output/`.

## 🚀 Актуальная версия резюме

<p align="center">
  <img src="output/Kalinin_Nikita_CV_1.png" alt="Скриншот резюме 1" width="48%" />
  <img src="output/Kalinin_Nikita_CV_2.png" alt="Скриншот резюме 2" width="48%" />
</p>

Текущая PDF-версия доступна здесь: [Kalinin_Nikita_CV.pdf](output/Kalinin_Nikita_CV.pdf)

## 📦 Зависимости

* [Python 3.13+](https://www.python.org/downloads/)
* [uv](https://docs.astral.sh/uv/getting-started/installation/)
* [Task](https://taskfile.dev/)

## ⚡ Быстрый старт

Установите все зависимости проекта:

```bash
task sync
```

Сгенерируйте резюме:

```bash
task render
```

Сгенерированные файлы будут сохранены в директории `output/`. По умолчанию основными выходными файлами являются:

* `output/Kalinin_Nikita_CV.pdf`
* `output/Kalinin_Nikita_CV.typ`

## 📁 Структура проекта

```text
cv/
  data.yaml      # Содержимое резюме: личные данные, образование, опыт, навыки и проекты
  design.yaml    # Конфигурация макета и дизайна RenderCV
output/          # Сгенерированные файлы резюме
Taskfile.yml     # Команды автоматизации для установки, проверки, генерации и релизов
pyproject.toml   # Метаданные Python-проекта и зависимости
```

## ✏️ Редактирование резюме

Обновляйте содержимое резюме в файле:

```text
cv/data.yaml
```

Настраивайте макет, типографику и стили в файле:

```text
cv/design.yaml
```

После внесения изменений заново сгенерируйте резюме:

```bash
task render
```
