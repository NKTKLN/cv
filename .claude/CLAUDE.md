# Claude instructions for this CV repository

This repository builds a RenderCV resume from YAML.

## Editable source

- `cv/data.yaml` is the canonical resume content.
- `cv/design.yaml` controls layout and styling.
- Do not edit generated files in `output/` directly.

## Evidence and rules

- `docs/resume-rules.md` contains the local resume quality standard.
- `projects/*.md` are source-of-truth project notes and should be used to ground claims.
- Use the Google X-Y-Z achievement formula already documented in `docs/resume-rules.md`.

## Commands

- Sync deps: `task sync`
- Validate YAML: `task validate`
- Render resume: `task render`
- Format YAML: `task fmt`

If commands fail because dependencies or internet access are unavailable, explain the failure and provide exact commands for the user to run locally.

## Local resume workflow skills

- `/cv-pipeline` — full workflow
- `/diagnoser` — audit before edits
- `/recruiter` — recruiter/JD match screen
- `/rewriter` — edit `cv/data.yaml`
- `/hiring-manager` — final skeptical review
- `/application-pack` — cover letter and outreach package

All skills are project-local. No external plugins or global Claude setup are required.

## Guardrails

- Do not fabricate metrics or experience.
- Preserve RenderCV schema.
- Keep recommendations and edits interview-defensible.
- Prefer concrete evidence over adjectives.
