## Why

The v0.1.2 lifecycle added `review.md` and `test-plan.md`, but the behavior is still too soft: `grill-with-docs` can be treated as an optional review note and TDD can degrade into after-the-fact verification. New projects need the Matt Pocock `grill-with-docs` and `tdd` skills embedded as real OpenSpec gates that Codex executes automatically.

## What Changes

- **BREAKING**: Replace the single post-design `review.md` gate with two explicit Matt-style gates: `grill.md` before `design.md`, and `design-review.md` after `design.md`.
- Vendor the upstream Matt Pocock `grill-with-docs` skill and adapt it to the intent-driven OpenSpec context model.
- Add a root `CONTEXT.md` glossary convention, with optional `CONTEXT-MAP.md` support for multi-context repositories.
- Vendor the upstream Matt Pocock `tdd` skill as the canonical project-local TDD discipline for `/opsx:apply`.
- Make `test-plan.md` define vertical RED/GREEN/REFACTOR slices instead of allowing broad “verification-first” placeholders.
- Update `/opsx:*` prompts, lifecycle skills, schema, templates, specs, docs, overlay checks, and installation scripts.
- Add a durable ADR for the Matt grill/TDD gate architecture and update `ARCHITECTURE.md`.

## Capabilities

Modified capabilities:

- `intent-driven-schema`
- `context-aware-review`
- `test-first-verification`
- `codex-opsx-workflow`
- `template-installation`
- `project-context-architecture`

## Impact

- Affected overlay files: `.codex/prompts/opsx-*`, `.codex/skills/grill-with-docs`, `.codex/skills/tdd`, `.codex/skills/openspec-*`.
- Affected OpenSpec schema files: `openspec/schemas/intent-driven/schema.yaml` and artifact templates.
- Affected docs: `README.md`, `README.ru.md`, `AGENTS.md`, `docs/lifecycle.md`, `openspec/README.md`, `ARCHITECTURE.md`, and `adr/README.md`.
- Affected scripts: `scripts/check-overlay`, `scripts/install-overlay`.
- Project context: `CONTEXT.md` becomes the glossary/domain-language file; `CONSTITUTION.md` remains policy/rules; `ARCHITECTURE.md` remains current architecture; `adr/` remains durable decisions.
- No external systems or secret values are needed.
