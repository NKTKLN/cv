# 🤵‍♂️ CV

This repository contains the source for generating a professional CV using **RenderCV**.
The CV content is defined in YAML and rendered into PDF using a Typst-based template.

## 📦 Dependencies

* [Python 3.13+](https://www.python.org/downloads/)
* [uv](https://docs.astral.sh/uv/getting-started/installation/)
* [Task](https://taskfile.dev/)

## 🛠️ Quick Start

1. Install dependencies:

```bash
task sync
```

2. Generate the CV:

```bash
task cv-render
```

The generated files will appear in the `./output/` directory (e.g., `output/cv.pdf`).

## 📝 Editing the CV

* To update content: edit `cv/data.yaml`
* To modify layout and styling: edit `cv/theme.yaml`
* Rebuild with: `task cv-render`
