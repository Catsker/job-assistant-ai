# 006 — Evals for job posting extraction

## Goal
A repeatable measurement of extraction quality.

## Scope
- Dataset format: `packages/evals/datasets/vacancies/<id>.json` with
  `{ "input": "...raw text...", "expected": { ...schema fields... } }`.
- Runner `pnpm eval vacancies`: runs extraction on every item, computes the metrics
  from `docs/spec.md`, prints a summary table, saves a JSON report to `reports/`.
- Report includes per-item failures so they can be inspected.
- Normalization helpers for skills (case, simple alias map).

## Acceptance criteria
- Runner works on the first 5 labeled items.
- Report contains model, prompt version, date, metrics, total cost.

## Notes
- **Labeling the dataset is done by the author** (20–30 postings).
- Metrics logic is AI core: the agent proposes, the author confirms.
- Never edit expected values to make results look better.
