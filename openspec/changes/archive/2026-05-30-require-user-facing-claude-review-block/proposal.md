## Why

Operational use showed that raw Claude review helper output and cost-only lines are not enough: users need a consistent, explicit post-review summary that exposes cost/budget, surfaces only user-actionable questions, and proves each reviewer finding was handled before later artifacts or checkpoints consume the result.

## What Changes

- Require every Claude artifact review invocation, including skipped or unavailable reviews, to be followed by a user-visible `## Claude Review` block from the assistant.
- Include review budget/cost status, user-facing questions, verdict/summary, and the assistant's disposition for findings in that block.
- Prevent checkpoint or dependent-artifact progress while actionable Claude findings remain unhandled.
- Expose configured budget cap in helper reports so assistant output can show both actual cost and the active cap.
- Extend overlay checks and documentation so the user-facing review contract remains covered by project-local regression checks.

## Capabilities

- Modify `claude-artifact-review` to require the user-facing `## Claude Review` block and handled-finding semantics after every reviewer invocation.
- Modify `codex-opsx-workflow` to require `/opsx:*` prompts and lifecycle skills to render that block rather than relying on raw helper output.
- Modify `overlay-update-safety` to require overlay checks that guard the review-block prompt/skill contract and helper budget-cap field.

## Impact

- Affected files: `.codex/prompts/opsx-apply.md`, `.codex/prompts/opsx-continue.md`, `.codex/prompts/opsx-ff.md`, `.codex/prompts/opsx-propose.md`, `.codex/skills/openspec-apply-change/SKILL.md`, `.codex/skills/openspec-continue-change/SKILL.md`, `.codex/skills/openspec-ff-change/SKILL.md`, `.codex/skills/openspec-propose/SKILL.md`, `scripts/openspec-claude-review`, `scripts/check-overlay`, `docs/lifecycle.md`, `docs/update-safety.md`, and related OpenSpec specs.
- No new external systems or secrets are required; `.secrets.local.env` must not be read.
- Architecture constraints: preserve ADR 0006's optional, structured, secret-safe Claude review helper boundary and ADR 0007's session controls and automatic safe checkpoint discipline.
- Verification: strict OpenSpec validation, helper/config checks, overlay health checks, prompt/skill text regression checks, diff hygiene, and tracked-secret checks.
