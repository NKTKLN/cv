---
name: application-pack
description: Create the application package around this CV: tailored cover letter, recruiter email, and short application answers using the current RenderCV resume and a target job description.
---

# Application Pack

Create job-application materials from the current resume repository.

## Inputs

- `cv/data.yaml` — current resume content.
- Optional job description pasted in chat.
- Optional company context supplied by the user.

## Rules

- Do not repeat the resume mechanically.
- Use specific fit evidence from the resume.
- Keep cover letters under one page, normally 180-300 words unless requested otherwise.
- If the company/role is unknown, ask for it or produce placeholders clearly marked.
- Do not invent motivation, referrals, company knowledge, or facts.

## Outputs available

Provide one or more of:

1. Cover letter.
2. Short recruiter email.
3. LinkedIn outreach message.
4. Answers to common application questions.
5. A final application checklist.

## Output format

```markdown
# Application Pack

## Cover letter
...

## Recruiter email
Subject: ...
Body: ...

## LinkedIn message
...

## Application checklist
- ...
```
