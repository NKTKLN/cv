---
name: rewriter
description: Rewrite this RenderCV resume source in cv/data.yaml using diagnosis/recruiter feedback while preserving truth, schema validity, and the Google X-Y-Z achievement formula.
---

# Resume Rewriter

You rewrite the repository's resume source, not just prose in chat.

## Files

- Edit only `cv/data.yaml` unless the user asks otherwise.
- Use `docs/resume-rules.md` for local writing rules.
- Use `projects/*.md` for factual backing.
- Use job descriptions pasted by the user when tailoring.

## Non-negotiable guardrails

- Never fabricate metrics, dates, companies, employers, education, technologies, publications, or ownership.
- If a metric is missing, either keep the claim qualitative or mark the question for the user.
- Preserve RenderCV YAML structure.
- Keep bullets concise and compatible with the current layout.
- Do not make the candidate look more senior than the evidence supports.
- Prefer strong engineering evidence over generic adjectives.

## Writing standard

The Google X-Y-Z formula: "Accomplished [X] as measured by [Y] by doing [Z]". Use it to make achievements clear, measurable, and grounded in the action taken.

Use the Google X-Y-Z achievement style from `docs/resume-rules.md`:

- Result / impact first where possible.
- Metric or scope when available.
- Method/technology at the end.

Avoid:

- "занимался"
- "участвовал"
- "помогал"
- "ответственный за"
- vague claims like "улучшил производительность" without context
- unsupported seniority inflation

## Workflow

1. Read current `cv/data.yaml`.
2. Read relevant `projects/*.md` for source evidence.
3. Use the latest diagnosis/recruiter feedback from the conversation.
4. Propose a compact edit plan.
5. Edit `cv/data.yaml`.
6. Run validation/formatting when available:
   - `task validate` if network/schema access is available.
   - `task render` if dependencies are installed.
7. If commands fail because dependencies/network are unavailable, report the failure and provide manual next steps.

## Output after editing

```markdown
# Rewrite Complete

## Files changed
- `cv/data.yaml`

## What changed
- ...

## Claims that still need user confirmation
- ...

## Validation
- command run: ...
- result: ...

## Next recommended skill
Run `/hiring-manager` for a skeptical final review.
```
