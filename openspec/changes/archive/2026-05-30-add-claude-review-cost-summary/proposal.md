## Why

Claude Code review already returns per-call `total_cost_usd` metadata when available, but Codex workflows do not consistently surface that cost to the user after each reviewer call or summarize total Claude spend at the end of `/opsx:apply`. Users need transparent cost visibility without treating a normal reported cost as a blocking condition.

## What Changes

- Surface Claude reviewer `total_cost_usd` after each reviewer invocation when Claude returns it.
- Ensure normal cost reporting never stops lifecycle progress by itself; only existing verdict/config/unavailable/budget-exhaustion policies may block.
- Add an apply-time aggregate Claude review cost summary so final `/opsx:apply` output reports total reviewer cost for the change/run when report data is available.
- Provide helper support and regression checks for summing persisted Claude review report costs.

## Capabilities

- Modify `claude-artifact-review` to require visible per-review cost reporting.
- Modify `codex-opsx-workflow` to require apply-final aggregate Claude review cost reporting.

## Impact

- Affected files: `scripts/openspec-claude-review`, `.codex/prompts/opsx-continue.md`, `.codex/prompts/opsx-ff.md`, `.codex/prompts/opsx-propose.md`, `.codex/prompts/opsx-apply.md`, `.codex/skills/openspec-continue-change/SKILL.md`, `.codex/skills/openspec-ff-change/SKILL.md`, `.codex/skills/openspec-propose/SKILL.md`, `.codex/skills/openspec-apply-change/SKILL.md`, `scripts/check-overlay`, and the relevant OpenSpec specs.
- No new external systems or secrets are required; `.secrets.local.env` must not be read.
- Architecture constraints: keep Claude review in the Codex overlay/helper layer, not in the OpenSpec CLI; preserve optional review, existing blocking policy, budget-exhaustion fail-safe, and automatic local checkpoint discipline.
- Verification: strict OpenSpec validation, helper shell syntax/config checks, cost-summary regression checks, overlay health checks, diff whitespace checks, and tracked-secret checks.
