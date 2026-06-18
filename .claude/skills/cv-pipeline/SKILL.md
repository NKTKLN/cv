---
name: cv-pipeline
description: Run the full resume improvement workflow for this repository using diagnoser, recruiter, rewriter, hiring-manager, and application-pack skills.
---

# CV Pipeline

Use this when the user wants the whole resume workflow in this repository.

## Pipeline

1. `/diagnoser`
   - Audit `cv/data.yaml` against `docs/resume-rules.md`.
   - If a job description exists, include JD gap analysis.
2. `/recruiter`
   - Simulate first-pass recruiter screen.
   - Extract missing keywords and objections.
3. `/rewriter`
   - Edit `cv/data.yaml` truthfully and preserve RenderCV schema.
   - Run `task validate` / `task render` when available.
4. `/hiring-manager`
   - Review the final rendered resume skeptically.
5. `/application-pack`
   - Produce cover letter or outreach materials if needed.

## Repository-specific facts

- Source file: `cv/data.yaml`
- Design file: `cv/design.yaml`
- Rules: `docs/resume-rules.md`
- Project evidence: `projects/*.md`
- Job description: pasted by the user
- Render command: `task render`
- Validate command: `task validate`
- Output PDF: `output/Kalinin_Nikita_CV.pdf`

## User-facing workflow prompt

If the user says "run the pipeline", proceed phase by phase. Do not skip straight to rewriting unless diagnosis and recruiter fit are already clear.

At each phase, summarize the decision and ask only when factual input is needed, such as missing metrics or target job context.
