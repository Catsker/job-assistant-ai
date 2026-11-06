# 001 — Scaffold the monorepo

## Goal
A working monorepo skeleton matching `CLAUDE.md`, with all quality checks passing.

## Scope
- pnpm workspaces + Turborepo, root `package.json` with scripts:
  `dev`, `build`, `test`, `lint`, `typecheck`, `db:generate`, `db:migrate`, `eval`.
- `tsconfig.base.json` (strict) extended by every package.
- `apps/web`: Next.js (App Router, TypeScript, `src/` directory).
- `packages/core`, `packages/db`, `packages/evals`: minimal packages with `src/index.ts`,
  importable as `@job-assistant/<name>` via `workspace:*`.
- Inside `packages/core/src` create only `llm/` and `vacancy/`. Other folders from
  `CLAUDE.md` are the target structure and are created in later stages.
- Do **not** create `apps/worker` yet (stage 4).
- ESLint + Prettier, Vitest with one trivial test per package.
- `.gitignore` (including `.env.local`), `.env.example` with `ANTHROPIC_API_KEY`
  and `DATABASE_URL`.

## Acceptance criteria
- `pnpm install` works from a clean clone.
- `pnpm dev` starts the Next.js app.
- `pnpm typecheck && pnpm lint && pnpm test` pass.
- `apps/web` can import something from `@job-assistant/core`.

## Notes
Pure boilerplate: the agent can implement it directly.
