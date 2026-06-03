## Context Read

- `openspec/changes/require-user-facing-claude-review-block/proposal.md`
- `openspec/changes/require-user-facing-claude-review-block/specs/claude-artifact-review/spec.md`
- `openspec/changes/require-user-facing-claude-review-block/specs/codex-opsx-workflow/spec.md`
- `openspec/changes/require-user-facing-claude-review-block/specs/overlay-update-safety/spec.md`
- `openspec/changes/require-user-facing-claude-review-block/grill.md`
- `openspec/changes/require-user-facing-claude-review-block/design.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`
- `adr/0003-formalize-project-context-entrypoints.md`
- `adr/0005-adopt-matt-grill-and-tdd-gates.md`
- `adr/0006-adopt-claude-artifact-review.md`
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md`
- `docs/lifecycle.md`
- `docs/update-safety.md`
- `scripts/openspec-claude-review`
- `scripts/check-overlay`
- `.codex/prompts/opsx-continue.md`
- `.codex/prompts/opsx-ff.md`
- `.codex/prompts/opsx-propose.md`
- `.codex/prompts/opsx-apply.md`
- `.codex/skills/openspec-continue-change/SKILL.md`
- `.codex/skills/openspec-ff-change/SKILL.md`
- `.codex/skills/openspec-propose/SKILL.md`
- `.codex/skills/openspec-apply-change/SKILL.md`

## Design Summary

- Keep Claude review mechanics in `scripts/openspec-claude-review` and conversation rendering in Codex prompts/skills.
- Add `budgetLimitUsd` to reports so the assistant can render configured `maxBudgetUsd`/none alongside actual cost availability.
- Require every review boundary to produce `## Claude Review` with `Budget / cost`, `User-facing questions`, and `Short summary` sections.
- Gate checkpoint/dependent progress on explicit disposition for every actionable Claude finding/question.
- Add docs and `scripts/check-overlay` checks to prevent prompt/skill/helper drift.

## Question Loop

### Q1: Should `scripts/openspec-claude-review` print the human `## Claude Review` block itself?

**Recommended answer:** No; keep the helper JSON-oriented and make prompts/skills responsible for user rendering.

**Rationale:** The helper is used by automation and persisted reports. Adding prose to the helper would make parsing harder and would mix machine and conversation responsibilities. ADR 0006 already places structured review output in the helper and Codex-facing behavior in the overlay guidance.

**Resolution:** The design remains helper-report + prompt/skill-rendered block.

### Q2: Which prompts/skills must be guarded by `scripts/check-overlay`?

**Recommended answer:** Guard the stage-producing or apply workflows that directly invoke `scripts/openspec-claude-review`: `opsx-continue`, `opsx-ff`, `opsx-propose`, `opsx-apply`, and their matching `openspec-*` skills.

**Rationale:** `opsx-new` and review-control prompts mention the helper for session/config status but do not review generated artifacts. Verification reads reports rather than invoking the reviewer. The user's minimum target aligns with call sites that create/consume reviewable artifacts.

**Resolution:** `scripts/check-overlay` will enforce the block rule on those eight direct review-call files and can be extended if future call sites are added.

## Design Findings

- **Finding:** The proposal requires skipped/unavailable reviews to display budget status, but helper reports currently omit the configured cap.  
  **Resolution:** Add `budgetLimitUsd` to base reports and check it in helper report-shape regression tests.
- **Finding:** A pass/warn verdict can still contain questions.  
  **Resolution:** Prompts/skills must classify questions into context-resolved versus user-required and stop on user-required questions.
- **Finding:** Checkpointing after Claude review can accidentally happen with unhandled non-blocking findings if wording only says to print cost.  
  **Resolution:** Add the explicit phrase `Do not checkpoint while any Claude finding is unhandled` to checked prompts/skills.
- **Finding:** Installed overlays use `docs/intent-driven-*.md` while the template source uses `docs/*.md`.  
  **Resolution:** `scripts/check-overlay` should support both path names for docs drift checks.

## Document Updates Applied

- No proposal/spec/design changes were needed after this review; existing artifacts already cover the required behavior and implementation approach.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None. The design is an incremental refinement of ADR 0006 and ADR 0007, with no new durable architecture decision.

## Open Questions

None.
