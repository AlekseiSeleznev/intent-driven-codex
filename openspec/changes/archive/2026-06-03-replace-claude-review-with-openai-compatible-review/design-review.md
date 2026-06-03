## Context Read

- `proposal.md`, delta specs, `grill.md`, and `design.md` for intended behavior and design.
- `CONSTITUTION.md` for secret handling and external-system access rules.
- `ARCHITECTURE.md`, `adr/0006-adopt-claude-artifact-review.md`, and `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` for current reviewer architecture.
- Polza API docs and unauthenticated model catalog metadata for base URL, chat completions endpoint, usage/cost fields, reasoning fields, and `deepseek/deepseek-v4-pro` support.

## Plan Summary

- Design cleanly separates provider-neutral review from legacy Claude review.
- It preserves review safety semantics while replacing the active invocation boundary.
- It avoids tracked secrets and uses local `.secrets.local.env`/environment variables only.
- It chooses prompt-only JSON as the compatibility default for DeepSeek Pro.

## Question Loop

### Q1: Is it safe to leave legacy Claude assets in place?

- **Recommended answer**: Yes, if all active lifecycle references move to artifact review and check-overlay prevents accidental Claude invocation guidance from returning.
- **Rationale**: User explicitly requested not killing Claude codebase yet. Retaining files reduces migration risk, but only if active prompts/skills/docs are updated.
- **Resolution**: Keep files, remove active call path, document as legacy-disabled.

### Q2: Is `deepseek/deepseek-v4-pro` acceptable despite missing advertised `response_format` in Polza metadata?

- **Recommended answer**: Yes, but default to prompt-only JSON and strict parser-side validation.
- **Rationale**: User requested DeepSeek Pro; Polza metadata shows the model exists and supports reasoning but not necessarily structured outputs.
- **Resolution**: Use `structuredOutputMode: prompt_only` by default. Add modes for future providers/models.

### Q3: Should live Polza tests be committed as reports?

- **Recommended answer**: No. Live smoke reports can be generated locally under an ignored session/test path or deleted after verification; OpenSpec review reports for real changes may be tracked only if they contain no secrets.
- **Rationale**: User asked to store connection parameters locally and not write to GitHub; live smoke should not create accidental tracked noise.
- **Resolution**: Local test credentials in `.secrets.local.env`; tracked config remains non-secret and disabled.

## Resolved Terms

- `Artifact Review` is the current provider-neutral user-facing label.
- `Claude Review` is legacy-disabled terminology only.

## Document Updates Applied

- Design already requires provider-neutral naming, local secret handling, prompt-only JSON default, and legacy Claude disablement.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

- Durable ADR required and should supersede ADR 0006's Claude-specific review decision and ADR 0007's Claude-specific session-control parts.

## Open Questions

None
