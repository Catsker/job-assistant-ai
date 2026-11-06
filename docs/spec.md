# Job Assistant — Specification

## Product

A personal tool that helps one user (the author) with job hunting. The user pastes
job postings, the app turns them into structured data, compares them with the user's
resume, explains skill gaps, and drafts cover letters.

Single user. No authentication until it is actually needed.

## Stages

1. **Job posting extraction** — paste text, get validated structured data, store it,
   measure extraction quality with evals. *(current stage)*
2. **Resume and matching** — parse the resume, compute a match score in code,
   use the LLM only to explain gaps; embeddings for similar skill names.
3. **Cover letters** — streamed generation grounded strictly in the resume,
   with an eval that catches invented claims.
4. **Production quality** — background worker with a queue, LLM call logging in the DB,
   caching, cost dashboard, deployment.
5. **Analytics (optional)** — most demanded skills across saved postings, missing skills.

## Stage 1 — Job posting extraction

### User flow

1. The user pastes the raw text of a job posting (optionally the source URL).
2. The app extracts structured data and shows it for review.
3. The posting is saved; the user can see a list of postings and open each one.

### Extracted data (draft schema)

| Field | Type | Notes |
|---|---|---|
| title | string | Job title as written |
| company | string \| null | |
| seniority | `intern` \| `junior` \| `middle` \| `senior` \| `lead` \| null | null if not stated or not clearly implied |
| workMode | `remote` \| `hybrid` \| `onsite` \| null | |
| employmentTypes | array of `full_time` \| `part_time` \| `contract` \| `b2b` | empty if not stated |
| locations | string[] | |
| salary | object \| null | `{ min, max, currency, period: month\|year\|hour, gross: boolean\|null }`; null if no salary given |
| minYearsOfExperience | number \| null | |
| requiredSkills | string[] | Technologies and skills marked as required |
| niceToHaveSkills | string[] | Marked as optional / a plus |
| languages | `{ language, level \| null }[]` | Human languages |

Core rule: **if information is not in the text, the field is null or empty — never a guess.**

The final schema is written by the author; this table is a starting point.

### Non-functional requirements

- Extraction uses the cheap model (see `CLAUDE.md`).
- Every LLM call logs model, prompt version, tokens, cost, and latency (console / JSON logs
  in stage 1; a DB table comes in stage 4).
- Invalid model output: one retry with the validation error, then a clear error in the UI.
- **Temporary exception:** in stage 1, extraction runs synchronously in the API route.
  It moves to the background worker in stage 4.

### Evals

Dataset: 20–30 real job postings, labeled by hand, varied on purpose (with and without
salary, different seniority levels, different languages, messy formatting).

Metrics, reported per field and overall:

- **Categorical fields** (seniority, workMode, employmentTypes): exact-match accuracy.
- **Skills**: precision, recall, and F1 after normalization (case, trivial aliases).
- **Salary**: exact match of min, max, currency, period.
- **Hallucination rate**: share of fields where the model returned a value
  but the expected value is null. This is the most important metric.

Each run is saved to `packages/evals/reports/` with the date, model, and prompt version,
so changes can be compared over time.

### Out of scope for stage 1

Resume, matching, cover letters, background worker, scraping job sites, authentication,
deployment.
