## Context Read

- `openspec/changes/require-user-facing-claude-review-block/proposal.md`
- `openspec/changes/require-user-facing-claude-review-block/specs/claude-artifact-review/spec.md`
- `openspec/changes/require-user-facing-claude-review-block/specs/codex-opsx-workflow/spec.md`
- `openspec/changes/require-user-facing-claude-review-block/specs/overlay-update-safety/spec.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
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

## Plan Summary

- Claude artifact review already has structured reports, optional session controls, cost surfacing, and budget-exhaustion fail-safe behavior.
- The observed gap is user-facing workflow output: cost-only lines and raw helper JSON do not force the assistant to tell the user what Claude asked, what findings were handled, or why it is safe to checkpoint.
- The new behavior must be a Codex-overlay contract in prompts/skills/docs/checks, not an OpenSpec CLI change.
- Helper reports should expose the configured budget cap so the required block can show actual cost and cap even for skipped or unavailable reviews.
- Checkpoints must remain safe: actionable Claude findings require a disposition before checkpoint or dependent progress.

## Question Loop

### Q1: Should the assistant print all Claude findings after every review?

**Recommended answer:** No. Print only cost/budget, user-required questions, verdict/summary, and a concise disposition of findings.

**Rationale:** The user explicitly requested not to dump every `mustFix`/`shouldFix`/question/warning unless it needs user participation. The important behavior is not verbosity; it is proving that findings were handled, deferred to a concrete place, declared non-actionable with a reason, or surfaced as blockers.

**Resolution:** Delta specs require concise summary and disposition, not full raw finding dumps.

### Q2: Should skipped/unavailable reviews still produce a `## Claude Review` block?

**Recommended answer:** Yes, when the workflow reaches a review invocation/decision boundary.

**Rationale:** The user asked to show budget/cost status even when review was skipped or unavailable. That gives a stable UI contract and avoids silently losing the review state in disabled/unavailable sessions.

**Resolution:** Delta specs require the block for successful, skipped, unavailable, parse/config-failed, and budget-exhaustion outcomes.

### Q3: Should unanswered Claude questions block even with `pass` or `warn` verdicts?

**Recommended answer:** Yes, but only when the question truly requires user input after Codex checks repository context and artifacts.

**Rationale:** A non-failing verdict can still contain a decision question. Hiding it would violate the user's request; blocking every question blindly would add unnecessary friction.

**Resolution:** Codex must resolve context-answerable questions itself, summarize the resolution, and stop only on questions that need the user.

### Q4: Does this require a durable ADR?

**Recommended answer:** No new durable ADR.

**Rationale:** This refines the existing Claude artifact review output contract inside ADR 0006 and ADR 0007 boundaries. It is reversible, local to the overlay, and does not introduce a new architecture boundary or external system.

**Resolution:** Capture rationale in the change-local ADR review only.

## Resolved Terms

- `Claude Review block`: the assistant-rendered `## Claude Review` section that summarizes a reviewer invocation for the user. It is workflow output, not raw helper JSON.
- `User-facing question`: a Claude question that cannot be resolved from repository context/artifacts and requires a user decision before progress.
- No `CONTEXT.md` update was needed because these are overlay workflow terms rather than project domain terms.

## Document Updates Applied

- Delta specs now require the user-facing block, budget/cost display, user-question filtering, finding disposition, and no-checkpoint-with-unhandled-findings behavior.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None. Existing ADR 0006 and ADR 0007 remain sufficient.

## Open Questions

None.
