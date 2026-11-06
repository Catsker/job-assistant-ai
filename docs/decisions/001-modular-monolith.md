# ADR 001 — Modular monolith in a pnpm monorepo

## Status
Accepted

## Context
The app has a web UI and, from stage 4, a background worker. Both need the same
domain logic, LLM client, and database access. The project is built by one person.

## Decision
A monorepo with deployable apps in `apps/` and shared libraries in `packages/`.
Domain and AI logic live in `packages/core`, organized by feature. All model calls go
through a single LLM client.

## Alternatives considered
- **Single Next.js app.** Simplest, but the worker would have to import code from
  inside the web app or duplicate it.
- **Microservices.** Separate services per feature (extraction, matching, letters).
  Rejected: one developer, one database, no independent scaling or team boundaries
  to justify the operational cost.

## Consequences
- More configuration (workspaces, per-package tsconfig) than a single app.
- Clear boundaries; `core` is testable without Next.js or a database.
- Splitting a service out later remains possible if a real need appears.
