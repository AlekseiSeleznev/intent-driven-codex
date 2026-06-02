## Context Read

- `openspec/changes/add-claude-review-cost-summary/proposal.md`
- `openspec/changes/add-claude-review-cost-summary/specs/claude-artifact-review/spec.md`
- `openspec/changes/add-claude-review-cost-summary/specs/codex-opsx-workflow/spec.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `adr/0006-adopt-claude-artifact-review.md`
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md`
- `scripts/openspec-claude-review`
- `.codex/prompts/opsx-apply.md`
- `.codex/skills/openspec-apply-change/SKILL.md`

## Plan Summary

- Claude review remains an optional Codex-overlay workflow, not an OpenSpec CLI feature.
- Existing helper behavior already captures Claude Code `total_cost_usd`, `duration_ms`, and `num_turns` in report `cost` metadata when available.
- The missing behavior is workflow visibility: Codex prompts/skills need to surface per-call cost after reviewer invocations and summarize aggregate reviewer cost at `/opsx:apply` completion.
- Cost reporting must be informational; it must not weaken verdict blocking, budget-exhaustion blocking, TDD, artifact gates, or checkpoint discipline.
- Aggregation should avoid new Claude calls and should be computable from persisted non-secret review reports when available.

## Question Loop

### Q1: Should aggregate apply cost be sourced from the current in-memory run, persisted reports, or both?

**Recommended answer:** Support both in workflow guidance, and add deterministic helper support for persisted reports as the durable fallback.

**Rationale:** A single Codex apply run can track costs returned by reviewer calls it just invoked, but future sessions and interrupted runs need a stable source. Persisted reports under `openspec/changes/<change>/reviews/` already exist as the auxiliary reviewer evidence store and do not require another Claude invocation.

**Resolution:** Use persisted report aggregation as the helper-supported mechanism, while instructing `/opsx:apply` to include current-run reviewer costs in the final summary when it has them.

### Q2: Should missing `total_cost_usd` block the workflow?

**Recommended answer:** No. Report cost unavailable and continue according to existing review verdict and policy.

**Rationale:** Claude Code output shape or future provider behavior may omit cost metadata. Blocking because cost is unavailable would make review less robust and would contradict the user's request to print cost without interrupting. Existing blockers remain verdict/config/unavailable/budget-exhaustion policies.

**Resolution:** Specs require missing cost to be non-blocking.

### Q3: Does this require a durable ADR?

**Recommended answer:** No new durable ADR.

**Rationale:** The decision is an incremental refinement of ADR 0006's structured reports and ADR 0007's session review controls. It does not introduce a new architecture boundary, external system, or hard-to-reverse trade-off.

**Resolution:** Capture the decision in change-local `adr.md`; do not create a new top-level ADR.

## Resolved Terms

- `Claude reviewer cost`: the cost metadata returned by Claude Code for artifact-review calls, especially `total_cost_usd`. It excludes unrelated Codex execution, OpenSpec CLI work, git operations, and manual user costs.
- No glossary update was needed because this is implementation/workflow terminology, not stable domain language for `CONTEXT.md`.

## Document Updates Applied

- Delta specs require per-call cost visibility and apply-final aggregate cost reporting.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None. This is covered by ADR 0006 and ADR 0007.

## Open Questions

None.
