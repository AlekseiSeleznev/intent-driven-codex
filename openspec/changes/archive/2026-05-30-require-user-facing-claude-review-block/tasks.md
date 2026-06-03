## 1. Helper Report Contract

- [x] 1.1 RED: Run a public `scripts/openspec-claude-review --parse-file` fixture check proving reports currently do not expose `budgetLimitUsd`.
- [x] 1.2 GREEN: Add `budgetLimitUsd` to the shared reviewer base report using the effective stage `maxBudgetUsd` value.
- [x] 1.3 GREEN: Add or update overlay checks so reviewer report-shape regressions fail when `budgetLimitUsd` is missing.

## 2. Prompt, Skill, and Documentation Contract

- [x] 2.1 RED: Run text checks proving relevant prompts/skills/docs do not yet require a user-facing `## Claude Review` block with budget, user-question, summary, and finding-disposition rules.
- [x] 2.2 GREEN: Update direct reviewer-call prompts and skills to require the `## Claude Review` block after every reviewer invocation or skipped/unavailable outcome.
- [x] 2.3 GREEN: Update lifecycle/update-safety docs to describe the review block, budget/cost reporting, user-question filtering, and checkpoint blocker behavior.
- [x] 2.4 GREEN: Extend `scripts/check-overlay` to enforce the prompt/skill/docs markers for future overlay updates.

## 3. Verification, Spec Sync, and Archive

- [x] 3.1 Run helper checks: `bash -n scripts/openspec-claude-review scripts/check-overlay` and `scripts/openspec-claude-review --validate-config`.
- [x] 3.2 Run OpenSpec checks: `openspec validate require-user-facing-claude-review-block --strict`, `openspec validate --all --strict`, and `openspec schema validate intent-driven`.
- [x] 3.3 Run full overlay and hygiene checks: `scripts/check-overlay`, `git diff --check`, tracked-secret check, and `git status --short`.
- [x] 3.4 Update `test-plan.md` evidence and mark tasks complete only after matching RED/GREEN and verification evidence exists.
- [x] 3.5 Confirm the change is archive-ready after all checks pass; archive is a separate lifecycle state change.
