# ADR Review: fix-artifact-review-session-config

## In-force ADRs Considered

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`
- `adr/0003-formalize-project-context-entrypoints.md`
- `adr/0005-adopt-matt-grill-and-tdd-gates.md`
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md`
- `adr/0008-adopt-openai-compatible-artifact-review.md`

## Decision Review

This change refines ADR 0008 session-control behavior by making the artifact review helper consume session-effective config by default. It does not introduce a new provider, model policy, external system, or long-term architecture direction.

## Durable ADR Needed?

No new durable ADR is required. The active OpenAI-compatible review architecture remains in force; this is a bug fix to session-state integration.

## Architecture Snapshot Impact

Update `ARCHITECTURE.md` to clarify that ordinary artifact-review helper runs use session-effective config unless `--config`, `--raw-config`, or `--ignore-session` is used.

## Superseded ADRs

None.
