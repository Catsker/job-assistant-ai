# Job Assistant

A portfolio project for learning AI engineering. The app helps with job hunting:
it parses job postings into structured data, compares them against a resume,
highlights missing skills, and generates cover letters.

Detailed requirements: `docs/spec.md`. Current stage tasks: `docs/tasks/`.
Architecture decisions and their rationale: `docs/decisions/`.

## Stack

- TypeScript (strict), Node.js, pnpm workspaces + Turborepo
- Next.js (App Router) for the UI and thin API routes
- PostgreSQL + pgvector, Drizzle ORM
- pg-boss for background jobs on top of Postgres (no Redis)
- Anthropic SDK (Claude), Zod to validate every model response
- Vitest for tests

## Structure

```
apps/
  web/        Next.js: pages, components, API routes. No business logic.
  worker/     Background process: job parsing, embeddings, batch tasks.
packages/
  core/       Domain logic and everything AI-related.
    src/llm/          The only place models are called: client, retries, logging.
    src/vacancy/      Job posting schema, extraction, prompts.
    src/resume/       Resume schema, extraction, prompts.
    src/matching/     Deterministic scoring + LLM explanation of skill gaps.
    src/cover-letter/ Cover letter generation.
    src/embeddings/   Fetching and comparing embeddings.
  db/         Drizzle schema, migrations, DB client.
  evals/      Datasets and runners for quality evaluation.
docs/         Spec, tasks, ADRs.
```

## Commands

```
pnpm dev            # web + worker in dev mode
pnpm build
pnpm test           # unit tests, no real LLM calls
pnpm lint
pnpm typecheck
pnpm db:generate    # generate a migration from the schema
pnpm db:migrate     # apply migrations
pnpm eval <name>    # run an eval (costs money, see below)
```

Before considering a task done: `pnpm typecheck && pnpm lint && pnpm test`.

## Architecture rules

- Package boundaries: `apps/*` depend on `packages/*`, never the other way around.
  `core` knows nothing about Next.js and does not import `db` directly: data access
  is passed in through interfaces (repositories).
- All model calls go through `packages/core/src/llm` only. No direct imports of the
  Anthropic SDK anywhere else.
- Every model call is logged: model, prompt version, input and output tokens,
  cost, latency, success or error.
- Every model response is validated with a Zod schema. On an invalid response, retry
  once with the validation error included, then return a clear error to the caller.
- Long-running LLM operations (more than a couple of seconds, or batch work) run in
  `worker` via the queue. The API route enqueues the job and immediately returns its id.
- Anything that can be computed in code (stack overlap, seniority comparison, salary
  ranges) is computed in code, not by the model.

## Working with models

- Models are configured in `packages/core/src/llm/models.ts`, overridable via env.
  Data extraction uses a fast, cheap model (`claude-haiku-5-5`);
  analysis and text generation use `claude-sonnet-5-5`.
- Prompts live in separate files next to their module (`prompts/*.ts`) and export
  the text plus a version constant. Changing a prompt means bumping its version.
- The model must not invent data. Schema fields that are absent from the source
  must be `null`, not a guess. Cover letters may only use facts from the resume.

## Tests and evals

- Unit tests never call real APIs: the LLM client is mocked.
- Evals live in `packages/evals`: `datasets/<name>/*.json` (input + expected output)
  and runners in `src/`. Results are saved to `reports/` with the date and prompt version.
- Evals cost real money. Do not run them unless explicitly asked.
- **Never change expected values in datasets to make an eval pass.** If a label looks
  wrong, flag it and explain why.

## How to work in this project

- This is a learning project: the author writes the AI core themselves to learn.
  For prompt files, model-facing Zod schemas, matching logic, and evals —
  **propose changes with an explanation, but do not apply them without confirmation**.
  Boilerplate (UI, CRUD, infrastructure setup) can be written directly.
- Before implementing a task, show a short plan and the list of files you will change.
- Do not add new dependencies without asking first.
- Secrets go only in `.env.local` (listed in `.gitignore`). Variable template: `.env.example`.
- Code, identifiers, comments, and docs are in English.
- Small commits following Conventional Commits (`feat:`, `fix:`, `chore:`, etc.).
