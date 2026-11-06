# 005 — API and UI for job postings

## Goal
The user can paste a posting, see the extracted result, and browse saved postings.

## Scope
- API route: accepts raw text (+ optional URL), validates input, calls
  `extractVacancy`, saves the result, returns it.
- Page "Add posting": textarea, optional URL, submit, loading state,
  readable display of the extracted fields, clear error state.
- Page "Postings": list with title, company, seniority, work mode, salary.
- Page "Posting": all extracted fields + raw text.

## Acceptance criteria
- Full flow works end to end against the real database and model.
- The API route contains no business logic beyond validation and calling `core`.

## Notes
Boilerplate: the agent can implement it directly.
