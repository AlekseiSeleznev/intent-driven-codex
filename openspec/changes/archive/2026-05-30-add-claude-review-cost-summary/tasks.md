## 1. Helper Cost Metadata Behavior

- [x] 1.1 RED: Add/run a parse-file fixture check proving `scripts/openspec-claude-review --parse-file` currently fails to preserve `cost.total_cost_usd` from a Claude Code envelope.
- [x] 1.2 GREEN: Add centralized cost extraction in `scripts/openspec-claude-review` and use it for real Claude results, parse-file results, and budget-exhausted reports.
- [x] 1.3 RED: Add/run a persisted-report aggregate check proving `scripts/openspec-claude-review --cost-summary --change <change>` is currently unavailable.
- [x] 1.4 GREEN: Implement `--cost-summary --change <change>` so it sums numeric `cost.total_cost_usd` values from `reviews/*.json` without requiring `--stage`, loading reviewer config, or invoking Claude.

## 2. Workflow Cost Visibility Guidance

- [x] 2.1 RED: Add/run text or overlay checks proving reviewer-call prompts/skills do not yet require per-call `Claude review cost` output and `/opsx:apply` does not yet require final `Total Claude review cost` output.
- [x] 2.2 GREEN: Update reviewer-call prompts and skills to display per-call cost after each Claude review invocation, report unavailable cost when metadata is missing, and treat cost output as informational/non-blocking.
- [x] 2.3 GREEN: Update `/opsx:apply` prompt and `openspec-apply-change` skill final-output guidance to include aggregate Claude artifact-review cost, sourced from current-run costs when tracked or from `--cost-summary` persisted reports.

## 3. Regression Coverage and Verification

- [x] 3.1 Extend `scripts/check-overlay` with cost preservation and cost summary regression coverage that runs without contacting Claude or reading `.secrets.local.env`.
- [x] 3.2 Run helper checks: `bash -n scripts/openspec-claude-review` and `scripts/openspec-claude-review --validate-config`.
- [x] 3.3 Run OpenSpec checks: `openspec validate add-claude-review-cost-summary --strict` and `openspec schema validate intent-driven`.
- [x] 3.4 Run full overlay and hygiene checks: `scripts/check-overlay`, `git diff --check`, tracked-secret check, and `git status --short`.
- [x] 3.5 Update the test-plan evidence log and mark tasks complete only after matching RED/GREEN or verification evidence exists.
