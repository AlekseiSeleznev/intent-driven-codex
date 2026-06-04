## Why

The current optional artifact reviewer is Claude Code-specific: lifecycle prompts ask about Claude review, session state stores `claudeReview`, and the helper shells out to `claude -p`. The user wants Claude review fully disabled from intent-driven workflow while preserving the review capabilities through a configurable OpenAI-compatible API reviewer that can use providers such as Polza.ai and models such as DeepSeek Pro.

## What Changes

- **BREAKING**: Intent-driven lifecycle commands stop asking about, enabling, or invoking Claude artifact review.
- Add a provider-neutral OpenAI-compatible artifact reviewer with configurable base URL, API key environment variable name, model, effort/reasoning options, stage policy, blocking policy, budget/cost reporting, and report persistence.
- Preserve the existing review behavior shape: secret-safe artifact bundles, structured verdicts, must-fix/should-fix/questions, user-facing review summaries, dependent-artifact gating, persisted reports, and aggregate cost summaries.
- Keep Claude review code/config/prompts in the codebase as disabled legacy assets for now, but remove them from the active intent-driven path and documentation as the current reviewer.
- Update README, architecture snapshot, diagrams, specs, prompts, skills, session-state helper, overlay checks, and installed-project assets to describe OpenAI-compatible artifact review instead of Claude review.
- Store local Polza.ai reviewer connection settings only in ignored `.secrets.local.env` or local session state, never in Git-tracked artifacts.

## Capabilities

- Add `artifact-review` for the new OpenAI-compatible review behavior.
- Modify `claude-artifact-review` to define Claude review as legacy-disabled rather than active lifecycle behavior.
- Modify `intent-driven-schema` so lifecycle guidance references provider-neutral artifact review reports and session overlays.
- Modify `template-installation` so installed docs, prompts, helpers, and check-overlay expectations use the new artifact reviewer controls.

## Impact

- Affected scripts: `scripts/openspec-artifact-review`, `scripts/openspec-session-state`, `scripts/check-overlay`, install/repair assets, and legacy `scripts/openspec-claude-review` call sites.
- Affected Codex overlay: `.codex/prompts/opsx-*`, `.codex/skills/openspec-*`, and `.codex/openspec-artifact-review.json`.
- Affected docs/context: `README.md`, `ARCHITECTURE.md`, `adr/README.md`, and a new durable ADR superseding Claude-specific parts of ADR 0006/0007.
- External system: Polza.ai OpenAI-compatible API via `https://polza.ai/api/v1`; local secret variable `POLZA_AI_API_KEY` only.
- Verification must include OpenSpec validation, helper dry-run/parse/config tests, local live smoke review when credentials are available, `scripts/check-overlay`, and git secret checks.
