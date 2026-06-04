## Context Read

- `openspec/changes/add-claude-review-cost-summary/proposal.md`
- `openspec/changes/add-claude-review-cost-summary/specs/claude-artifact-review/spec.md`
- `openspec/changes/add-claude-review-cost-summary/specs/codex-opsx-workflow/spec.md`
- `openspec/changes/add-claude-review-cost-summary/grill.md`
- `openspec/changes/add-claude-review-cost-summary/design.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `adr/0006-adopt-claude-artifact-review.md`
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md`
- `scripts/openspec-claude-review`
- `scripts/check-overlay`
- `.codex/prompts/opsx-apply.md`
- `.codex/skills/openspec-apply-change/SKILL.md`

## Design Summary

- Add a centralized cost extraction function for Claude Code result metadata.
- Preserve costs under the existing report `cost` field for real, parse-file, and budget-exhaustion paths.
- Add `--cost-summary --change <change>` to aggregate persisted report costs without invoking Claude.
- Update Codex prompt/skill guidance to display per-call reviewer cost and final apply aggregate cost.
- Keep cost reporting informational; existing blockers and gates remain authoritative.

## Question Loop

### Q1: Should the cost-summary helper validate reviewer config before reading persisted reports?

**Recommended answer:** No; it should bypass reviewer config loading/validation.

**Rationale:** Final apply cost reporting should remain available even when Claude review is disabled, config is being edited, or a user is summarizing historical reports. Report aggregation reads only existing JSON files and does not need model, effort, budget, or Claude executable state.

**Resolution:** Design was updated to state that `--cost-summary` must not validate reviewer stage configuration and should run even when the reviewer config is disabled or temporarily invalid.

## Design Findings

- **Finding:** Parse-file review currently normalizes verdict output but does not copy top-level Claude Code `total_cost_usd`, so tests could miss cost propagation regressions.
  - **Resolution:** Add parse-file cost preservation and check-overlay coverage.
- **Finding:** Persisted-report totals may include pre-apply artifact reviews, not only calls made during the current apply run.
  - **Resolution:** Workflow output must label the aggregate source clearly, e.g. current-run total when tracked by Codex, or persisted report total for the change when using `--cost-summary`.
- **Finding:** Cost metadata must not weaken budget-exhaustion handling.
  - **Resolution:** Specs and design keep budget exhaustion as a blocking fail-safe; the cost line is additional context only.

## Document Updates Applied

- Updated `design.md` to require `--cost-summary` to bypass reviewer config validation and to work from persisted reports without invoking Claude.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None. ADR 0006 and ADR 0007 already cover the architecture boundary and session review controls; this change is an incremental reporting behavior.

## Open Questions

None.
