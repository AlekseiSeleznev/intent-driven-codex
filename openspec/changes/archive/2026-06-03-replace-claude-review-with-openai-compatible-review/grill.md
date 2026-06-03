## Context Read

- `CONSTITUTION.md` — secret boundary: real API keys only in ignored `.secrets.local.env` or local environment; tracked files may name variables only.
- `ARCHITECTURE.md` — current snapshot still shows `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, and `claude -p` as the optional artifact reviewer path.
- `adr/0006-adopt-claude-artifact-review.md` — adopted Claude Code print-mode review and allows replacement if another reviewer API becomes available or cost/latency is too high.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — adopted session-scoped Claude review controls; notes provider-neutral session control as a future supersession condition.
- `README.md`, `.codex/prompts/opsx-*`, `.codex/skills/openspec-*`, `scripts/openspec-claude-review`, `scripts/openspec-session-state` — current lifecycle call sites and docs are Claude-specific.
- Polza.ai docs — OpenAI-compatible API uses `base_url="https://polza.ai/api/v1"`; chat completions post to `/chat/completions`; API key is passed with `Authorization: Bearer`; response usage may include `cost`/`cost_rub`; reasoning options include effort levels.
- Polza model catalog — `deepseek/deepseek-v4-pro` exists and is exposed through `/api/v1/chat/completions`.

## Plan Summary

- Replace the active reviewer path with an OpenAI-compatible artifact review helper while retaining legacy Claude files as disabled compatibility assets.
- Move active tracked config and session state from Claude-specific names to provider-neutral artifact review names.
- Preserve the existing useful review semantics: stage controls, secret-safe bundles, structured findings, blocking policy, user-facing summary, report persistence, and cost aggregation.
- Use Polza.ai + `deepseek/deepseek-v4-pro` as the local configured smoke target via ignored `.secrets.local.env`; tracked defaults stay safe and disabled.
- Update architecture/docs/diagrams/ADRs so current project context no longer presents Claude as the active reviewer.

## Question Loop

### Q1: Should current lifecycle commands keep any automatic Claude fallback?

- **Recommended answer**: No. Keep Claude assets in the repository as legacy-disabled, but never ask for or invoke Claude from current lifecycle flows.
- **Rationale**: The user explicitly said “claude review полностью отключаем, но не убиваем из кодовой базы”. Keeping fallback would violate “полностью отключаем”.
- **Resolution**: Accepted from user instruction. Current lifecycle will use only provider-neutral artifact review when enabled.

### Q2: Should the new helper use Responses API or Chat Completions API?

- **Recommended answer**: Use Chat Completions first.
- **Rationale**: Polza documents OpenAI-compatible Chat Completions at `https://polza.ai/api/v1/chat/completions` and exposes model catalog endpoints with `/api/v1/chat/completions` support. Chat Completions is also the broadest compatibility target for non-OpenAI providers.
- **Resolution**: Use Chat Completions for this change; keep config generic enough to add another endpoint type later.

### Q3: Should structured output be hard-required through provider `response_format`?

- **Recommended answer**: No. Use `response_format` only when configured/supported; otherwise enforce JSON through the system prompt and validate parser-side.
- **Rationale**: Polza model metadata for `deepseek/deepseek-v4-pro` lists reasoning/tool parameters but not `response_format`; requiring response_format could break the exact requested model.
- **Resolution**: Implement `structuredOutputMode: auto|json_schema|json_object|prompt_only|off`, with default `prompt_only` for broad compatibility.

### Q4: What icon replaces Claude in README diagrams?

- **Recommended answer**: Use a neutral magnifying glass label, `🔍 Artifact Reviewer`, rather than a provider logo.
- **Rationale**: The active reviewer is provider-neutral and configurable; a magnifying glass communicates review/inspection without tying the architecture to a vendor.
- **Resolution**: README/architecture diagrams should show `🔍 Artifact Reviewer` or `OpenAI-compatible reviewer`, not Claude.

## Resolved Terms

- **Artifact reviewer**: provider-neutral optional reviewer that inspects OpenSpec artifacts and returns structured findings. No glossary update needed because this is project architecture terminology rather than domain vocabulary.
- **Legacy Claude review**: retained code/config/prompts that are no longer invoked by intent-driven lifecycle. No `CONTEXT.md` update needed.

## Document Updates Applied

- Proposal and delta specs define active OpenAI-compatible review and legacy-disabled Claude behavior.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

- Durable ADR required: supersede the Claude-specific parts of ADR 0006 and ADR 0007 with provider-neutral OpenAI-compatible artifact review. This is hard to reverse, surprising without rationale, and a real trade-off between preserving Claude-specific implementation and adopting a configurable provider boundary.

## Open Questions

None
