## TDD Strategy

Use the project-local `tdd` skill with vertical command-based slices because this change affects Bash/Python helper behavior and Codex prompt/skill guidance. Each behavior is verified through public interfaces: `scripts/openspec-claude-review`, `scripts/check-overlay`, `openspec validate`, and text checks against shipped prompt/skill files. Production/helper changes must follow RED -> GREEN -> REFACTOR per slice before marking tasks complete.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| 1. Preserve per-call cost from parsed Claude result | `scripts/openspec-claude-review --parse-file` emits a report with `cost.total_cost_usd` when the Claude Code envelope contains `total_cost_usd` | Run a temp fixture with `structured_output` and top-level `total_cost_usd`, then assert `.cost.total_cost_usd == 0.012345`; before implementation the assertion fails because parse-file drops cost | Same command passes and report contains `cost.total_cost_usd`, `duration_ms`, and `num_turns` when present | Cost extraction is centralized and does not duplicate ad-hoc key copying |
| 2. Summarize persisted review report costs | `scripts/openspec-claude-review --cost-summary --change <change>` reads `reviews/*.json` and returns JSON aggregate without Claude | Run against a temp/smoke change with two costed reports and one missing-cost report; before implementation CLI rejects `--cost-summary` or cannot run without `--stage` | Same command passes with `totalCostUsd` equal to the sum, `costedReviewCount` for numeric costs, and `missingCostCount` for reports without numeric cost | Summary mode does not load/validate reviewer stage config, does not invoke Claude, and tolerates missing/malformed cost values |
| 3. Surface cost in workflow guidance | Codex prompt/skill files instruct agents to display per-call cost and final apply aggregate cost | Text check fails because `.codex/prompts/opsx-apply.md`, `.codex/skills/openspec-apply-change/SKILL.md`, and reviewer-call prompts/skills lack explicit `Claude review cost` / `Total Claude review cost` guidance | Text check passes and guidance states cost output is informational and non-blocking | Wording stays concise and consistent across prompts and skills |
| 4. Overlay regression coverage | `scripts/check-overlay` covers cost preservation and cost summary without contacting Claude | `scripts/check-overlay --no-smoke` or targeted inserted fixture fails before checks/implementation are present | Full `scripts/check-overlay` passes, including new cost fixture coverage | No test fixture reads `.secrets.local.env` or requires `claude` authentication |

## Mocking / Boundary Policy

- Do not mock internal helper functions. Exercise `scripts/openspec-claude-review` as the public interface.
- Use JSON fixture files to represent Claude Code envelopes; this is a boundary fixture, not an internal mock.
- Do not invoke real Claude for automated verification in this change unless the user explicitly asks; cost reporting tests must run without external credentials.
- Do not read `.secrets.local.env`.

## Required Checks

- Targeted RED/GREEN commands for each TDD slice above.
- `bash -n scripts/openspec-claude-review`
- `scripts/openspec-claude-review --validate-config`
- `openspec validate add-claude-review-cost-summary --strict`
- `openspec schema validate intent-driven`
- `scripts/check-overlay`
- `git diff --check`
- Tracked secret check: `git ls-files -- '.secrets.local.env' '.secrets.*.local.env' '.env.local' '.env.*.local'`
- `git status --short`

## Evidence Log

- Slice 1 RED: targeted parse-file fixture with top-level `total_cost_usd` failed because `.cost` was absent (`AssertionError: None`).
- Slice 1 GREEN: same fixture passed after centralized cost extraction; output included `{'duration_ms': 2222, 'num_turns': 1, 'total_cost_usd': 0.012345}`.
- Slice 2 RED: `scripts/openspec-claude-review --cost-summary --change <tmp>` exited 2 because `--cost-summary` was unavailable.
- Slice 2 GREEN: same public interface passed on three persisted review reports with `totalCostUsd=0.035`, `costedReviewCount=2`, and `missingCostCount=1`.
- Slice 3 RED: grep checks for `Claude review cost` in reviewer-call prompts/skills and `Total Claude review cost` in apply prompt/skill failed with exit 1.
- Slice 3 GREEN: same grep checks passed after prompt/skill guidance updates.
- Slice 4 RED: `grep -q -- "--cost-summary" scripts/check-overlay` failed because overlay coverage did not yet include cost-summary regression checks.
- Slice 4 GREEN: `bash -n scripts/check-overlay`, `grep -q -- "--cost-summary" scripts/check-overlay`, and `grep -q "cost metadata" scripts/check-overlay` passed after adding fixture coverage.
- Final helper checks passed: `bash -n scripts/openspec-claude-review` and `scripts/openspec-claude-review --validate-config`.
- Final OpenSpec checks passed: `openspec validate add-claude-review-cost-summary --strict` and `openspec schema validate intent-driven`.
- Final overlay/hygiene checks passed: full `scripts/check-overlay`, `git diff --check`, tracked-secret check, and clean `git status --short --branch` before evidence update.

## TDD Exceptions

None.
