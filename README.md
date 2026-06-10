# 📄 CV

This repository contains the source files and automation required to build a professional CV with [RenderCV](https://github.com/rendercv/rendercv). The CV content and design are maintained as YAML files, while the final PDF and Typst outputs are generated into the `output/` directory.

## 🚀 Latest CV

The current PDF version is available here:

[Kalinin_Nikita_CV.pdf](output/Kalinin_Nikita_CV.pdf)

## 📦 Dependencies

* [Python 3.13+](https://www.python.org/downloads/)
* [uv](https://docs.astral.sh/uv/getting-started/installation/)
* [Task](https://taskfile.dev/)

## ⚡ Getting Started

Install all project dependencies:

```bash
task sync
```

Generate the CV:

```bash
task render
```

The generated files will be written to the `output/` directory. By default, the primary outputs are:

* `output/Kalinin_Nikita_CV.pdf`
* `output/Kalinin_Nikita_CV.typ`

## 📁 Project Structure

```text
cv/
  data.yaml      # CV content: personal details, education, experience, skills, and projects
  design.yaml    # RenderCV layout and design configuration
output/          # Generated CV artifacts
Taskfile.yml     # Automation commands for setup, validation, rendering, and release tasks
pyproject.toml   # Python project metadata and dependencies
```

## ✏️ Editing the CV

Update the CV content in:

```text
cv/data.yaml
```

Adjust the layout, typography, and styling in:

```text
cv/design.yaml
```

After making changes, regenerate the CV:

```bash
task render
```
