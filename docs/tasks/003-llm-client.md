# 003 — LLM client

## Goal
The single entry point for all model calls, in `packages/core/src/llm`.

## Scope
- `models.ts`: model ids for "extraction" and "generation", overridable via env.
- `pricing.ts`: price per million input/output tokens per model, used to compute cost.
- `client.ts`: a function that takes a system prompt, user content, a Zod schema,
  a prompt version, and a model role, and returns validated typed data.
  - Uses tool use / structured output to get JSON.
  - Validates with Zod; on failure retries once, passing the validation error back.
  - Logs model, prompt version, tokens, cost, latency, and outcome.
- The client is injectable/mockable for unit tests.

## Acceptance criteria
- Unit tests with a mocked SDK cover: valid response, invalid then valid, invalid twice.
- No other file imports the Anthropic SDK.

## Notes
Check current Anthropic SDK docs for the structured output approach and model prices
instead of relying on memory.
