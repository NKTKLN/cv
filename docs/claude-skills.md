# Claude Skills workflow for this CV repository

This repository is self-contained: all Claude Code skills live in `.claude/skills/` and do not require external plugins, GitHub installs, or global `~/.claude` setup.

## Local commands

Use these in Claude Code from the repository root:

```text
/cv-pipeline
/diagnoser
/recruiter
/rewriter
/hiring-manager
/application-pack
```

## Recommended flow

```text
/cv-pipeline
```

Or manually:

```text
/diagnoser
/recruiter
/rewriter
/hiring-manager
/application-pack
```

## What each skill does

| Skill | Purpose | Edits files? |
|---|---|---|
| `/diagnoser` | Scores and audits the current CV against local rules and optional pasted JD | No |
| `/recruiter` | Simulates first-pass recruiter screen and keyword fit | No |
| `/rewriter` | Rewrites `cv/data.yaml` using the diagnosis and recruiter feedback | Yes, when asked |
| `/hiring-manager` | Reviews the final resume like a skeptical hiring manager | No |
| `/application-pack` | Creates cover letter, recruiter email, LinkedIn message, or application answers | No by default |
| `/cv-pipeline` | Runs the whole process in order | Depends on phase |

## Repository-specific context

Claude should use:

- `cv/data.yaml` as the resume source.
- `docs/resume-rules.md` as the writing standard.
- `projects/*.md` as factual evidence.
- Pasted job descriptions in Claude Code for target-role tailoring.

Generated files are in `output/`, but edits should happen in `cv/data.yaml`.

## Job description workflow

Paste the target job description directly into Claude Code, then run:

```text
/cv-pipeline
```

For a single phase:

```text
/recruiter
```

or:

```text
/rewriter
```

## Safety rules

- Do not fabricate metrics, dates, companies, publications, technologies, or ownership.
- Treat `cv/data.yaml` and `projects/*.md` as the source of truth.
- If a claim needs proof, ask for the missing information instead of inventing it.
- Keep all edits compatible with RenderCV YAML schema.
