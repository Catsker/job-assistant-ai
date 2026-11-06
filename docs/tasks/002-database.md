# 002 — Database and job postings table

## Goal
Postgres connected through Drizzle, with a table for job postings.

## Scope
- `packages/db`: Drizzle config, client, schema, migrations folder.
- Table `vacancies`: id, created_at, source_url (nullable), raw_text,
  extracted (jsonb), prompt_version, model.
  Storing the raw text allows re-running extraction after prompt changes.
- Enable the pgvector extension in a migration (used from stage 2).
- Repository functions: create, getById, list.

## Acceptance criteria
- `pnpm db:generate` and `pnpm db:migrate` work against `DATABASE_URL`.
- A small test or script inserts and reads a row.

## Notes
Database: Neon or Supabase free tier, or local Postgres in Docker — author's choice.
