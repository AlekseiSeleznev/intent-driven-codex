## In-force ADRs Considered

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — local Codex/OpenSpec overlay remains in force.
- `adr/0003-formalize-project-context-entrypoints.md` — root architecture/constitution/ADR context and local-secret boundaries remain in force.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — mandatory grill/design-review/TDD gates remain in force.
- `adr/0006-adopt-claude-artifact-review.md` — superseded in part because the active reviewer is no longer Claude Code-specific.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — superseded in part because active session review controls become provider-neutral.

## Grill and Design-Review Findings Considered

- User instruction requires Claude review fully disabled from active intent-driven workflow while retaining legacy code.
- Polza.ai provides OpenAI-compatible Chat Completions at `https://polza.ai/api/v1/chat/completions`.
- `deepseek/deepseek-v4-pro` exists in Polza model catalog but should default to prompt-only JSON because structured output support is not advertised for that model.
- README/architecture diagrams must replace Claude reviewer references with a neutral reviewer icon/label.

## Decisions Evaluated

### Decision: Active reviewer boundary

- **Chosen**: OpenAI-compatible Chat Completions helper `scripts/openspec-artifact-review`.
- **Rejected**: Keep `claude -p` as fallback. This conflicts with the user's “Claude review fully disabled” requirement.
- **Rejected**: Delete all Claude code immediately. This conflicts with the user's “не убиваем из кодовой базы” requirement.

### Decision: Session state model

- **Chosen**: Add provider-neutral `artifactReview` session state and keep `claudeReview` only as tolerated legacy state.
- **Rejected**: Reuse `claudeReview` for OpenAI-compatible providers. That would make docs and mental model misleading.

### Decision: Structured output strategy

- **Chosen**: Strict parser-side JSON with configurable provider `response_format` modes; default prompt-only for Polza DeepSeek Pro.
- **Rejected**: Hard-require JSON schema response_format. It may break the requested model/provider combination.

## New Durable ADRs Created

- `adr/0008-adopt-openai-compatible-artifact-review.md` is required.

## Superseded ADRs

- ADR 0006 remains historical but its Claude-specific active reviewer decision is superseded.
- ADR 0007 remains in force for automatic checkpointing, but its Claude-specific session review controls are superseded.

## Architecture Snapshot Update

`ARCHITECTURE.md` must be updated to show the OpenAI-compatible artifact reviewer, provider config, local `POLZA_AI_API_KEY` boundary, and legacy-disabled Claude assets.

## Rationale

The change replaces a provider-specific shell-out integration with a configurable provider-neutral boundary while preserving the OpenSpec review semantics already designed and tested. This is durable, architecture-significant, and needs a top-level ADR because future maintainers would otherwise wonder why Claude files remain while the active workflow ignores them.
