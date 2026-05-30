## TDD Strategy

Use the project-local `tdd` skill with command/text-check vertical slices because the change affects Bash/Python helper behavior, Markdown prompt/skill guidance, and overlay documentation. Each slice is verified through public project interfaces: `scripts/openspec-claude-review`, `scripts/check-overlay`, `openspec validate`, and repository text checks. No automated check may invoke real Claude or read `.secrets.local.env`.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| 1. Helper reports budget cap | `scripts/openspec-claude-review --parse-file ...` emits `budgetLimitUsd` in structured reports | Run a fixture through `--parse-file` and assert the JSON has top-level `budgetLimitUsd`; before implementation the assertion fails because the field is absent | Same command passes and report includes `budgetLimitUsd` with the stage `maxBudgetUsd` value or `null` | Field is produced by the shared base-report path, not duplicated per result type |
| 2. Prompt/skill review block contract | Relevant prompts/skills that invoke `scripts/openspec-claude-review` contain `## Claude Review`, `Budget / cost`, `maxBudgetUsd`, no-user-question phrase, and no-unhandled-finding checkpoint rule | Text checks fail because current prompt/skill wording only requires cost-line output | Text checks pass for `opsx-continue`, `opsx-ff`, `opsx-propose`, `opsx-apply`, and matching skills | Wording is concise and consistent across lifecycle entry points |
| 3. Docs and overlay drift checks | `scripts/check-overlay` enforces the review block contract and docs mention it | `scripts/check-overlay` or targeted greps fail because docs/checks lack the new markers | Full `scripts/check-overlay` passes and validates docs, prompts/skills, and helper report shape without Claude | Checks support template docs (`docs/lifecycle.md`) and installed docs (`docs/intent-driven-lifecycle.md`) |
| 4. OpenSpec capability sync | `openspec validate` and archive apply delta specs to canonical specs | Validation/archive would fail if requirements are malformed or tasks incomplete | `openspec validate require-user-facing-claude-review-block --strict`, `openspec validate --all --strict`, and archive/sync pass | Canonical specs receive only the intended requirements |

## Mocking / Boundary Policy

- Use JSON fixtures and `--parse-file` to represent Claude Code output; this is a boundary fixture and must not call real Claude.
- Do not mock internal helper functions; exercise `scripts/openspec-claude-review` and `scripts/check-overlay` as public interfaces.
- Text checks are acceptable because prompts/skills/docs are the shipped public contract for Codex behavior.
- Do not read `.secrets.local.env`.

## Required Checks

- Targeted RED/GREEN checks for `budgetLimitUsd`, prompt/skill markers, docs markers, and `scripts/check-overlay` coverage.
- `bash -n scripts/openspec-claude-review scripts/check-overlay`
- `scripts/openspec-claude-review --validate-config`
- `openspec validate require-user-facing-claude-review-block --strict`
- `openspec validate --all --strict`
- `openspec schema validate intent-driven`
- `scripts/check-overlay`
- `git diff --check`
- Tracked secret check: `git ls-files -- '.secrets.local.env' '.secrets.*.local.env' '.env.local' '.env.*.local'`
- `git status --short`

## Evidence Log

- Slice 1 RED: `scripts/openspec-claude-review --change require-user-facing-claude-review-block --stage proposal --parse-file <fixture>` returned a structured skipped report without `budgetLimitUsd` while session Claude review was disabled; the assertion failed with `budgetLimitUsd missing`.
- Slice 1 GREEN: the same public parse-file fixture produced a report with top-level `budgetLimitUsd: null` (exit `3` remained acceptable for the disabled session skip path).
- Slice 2 RED: targeted text checks reported missing `## Claude Review`, `Budget / cost`, `maxBudgetUsd`, `Вопросов, требующих участия пользователя: нет.`, and `Do not checkpoint while any Claude finding is unhandled` markers across reviewer-call prompts/skills and docs.
- Slice 2 GREEN: targeted text checks passed after updating `opsx-continue`, `opsx-ff`, `opsx-propose`, `opsx-apply`, and matching `openspec-*` skills plus lifecycle/update-safety docs.
- Slice 3 GREEN: `scripts/check-overlay` passed and reported documentation drift, prompt/skill Claude Review block rules, dry-run review, structured-output parse, budget-exhausted, and cost-summary checks without contacting Claude.
- Helper checks passed: `bash -n scripts/openspec-claude-review scripts/check-overlay` and `scripts/openspec-claude-review --validate-config`.
- OpenSpec checks passed: `openspec validate require-user-facing-claude-review-block --strict`, `openspec validate --all --strict`, and `openspec schema validate intent-driven`.
- Hygiene checks passed: `git diff --check`, tracked-secret check returned no tracked local secret files, and smoke changes from `scripts/check-overlay` were removed.

## TDD Exceptions

None.
