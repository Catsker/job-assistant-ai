# 004 — Job posting schema and extraction

## Goal
`extractVacancy(rawText)` returns validated structured data.

## Scope
- `vacancy/schema.ts`: Zod schema based on the table in `docs/spec.md`.
- `vacancy/prompts/extract.ts`: system prompt + `EXTRACT_VACANCY_PROMPT_VERSION`.
- `vacancy/extract.ts`: calls the LLM client with the schema and prompt.

## Acceptance criteria
- Works on 3–5 real postings checked by hand.
- Missing information comes back as null / empty, not guessed.

## Notes
**AI core — written by the author.** The agent reviews and suggests changes with
explanations but does not apply them without confirmation (see `CLAUDE.md`).
