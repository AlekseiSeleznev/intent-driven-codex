## Why

Intent-driven OpenSpec workflows currently require repeated manual checkpoint approval even when the user wants normal git discipline, and Claude artifact review is controlled only by project-level defaults rather than per-session intent. The workflow should make safe checkpointing the default while giving each session explicit, reversible control over Claude review enablement, model, and effort.

## What Changes

- **BREAKING**: Make OpenSpec lifecycle git checkpoints automatic by default for scaffold, artifact, and coherent apply task-group commits unless the user explicitly disables automatic git discipline or requests manual approval for the session.
- Preserve hard safety stops for dirty unrelated work, conflicting project rules, destructive operations, merges, pushes, archives, and one-time overrides that name the bypassed gate and accepted risk.
- Ask at the start of new Codex/OpenSpec sessions and key entry points such as `/opsx:new`, `/opsx:propose`, `/opsx:ff`, and `/opsx:apply` whether Claude artifact review should be enabled for the current session when no session decision is already recorded.
- Add `/opsx:*` commands to enable Claude review, disable Claude review, show review status, and set session-level stage/model/effort/budget/blocking overrides.
- Add a non-secret session overlay that merges over `.codex/openspec-claude-review.json` and can override global review settings for a concrete intent-driven OpenSpec session without replacing project defaults.
- Let session controls specify exact Claude model IDs or aliases plus effort per review stage, including distinct Opus/Sonnet profiles, while reusing the existing reviewer validation and fallback rules.
- Stop Claude review when Claude Code reports `error_max_budget_usd` / exhausted configured budget, report the blocker, and automatically disable Claude review for the current session so later lifecycle steps do not keep retrying a failing reviewer.
- Keep OpenSpec CLI ownership, project constitution preflight, grill/design-review gates, ADR review, TDD/test-plan gates, and no-secret handling unchanged.

## Capabilities

- Modified capability: `git-discipline` — default automatic checkpoint commits, opt-out/manual session mode, and unchanged hard safety gates.
- Modified capability: `codex-opsx-workflow` — session start and `/opsx:new`, `/opsx:propose`, `/opsx:ff`, `/opsx:apply`, and new review-control command behavior.
- New capability: `claude-review-session-controls` — session-level Claude artifact review enablement and per-stage overlay settings that extend the canonical `claude-artifact-review` capability.
- Modified capability: `intent-driven-schema` — if needed, clarify that Claude review session overlays are Codex-layer context and not OpenSpec lifecycle artifacts.

## Impact

- Affected workflow policy: current manual commit-approval language in `CONSTITUTION.md`, `AGENTS.md`, `openspec/specs/git-discipline/spec.md`, and related prompts/skills must be intentionally superseded or narrowed by this change.
- Affected overlay prompts/skills: `.codex/prompts/opsx-*`, `.codex/skills/openspec-*`, `.codex/skills/openspec-git-discipline/SKILL.md`, and related lifecycle guidance that currently stops for explicit checkpoint approval.
- Affected Claude review code/config: `.codex/openspec-claude-review.json`, `scripts/openspec-claude-review`, and any new non-secret session overlay storage or helper commands.
- Affected docs and architecture: `README.md`, `README.ru.md`, lifecycle/update docs, `ARCHITECTURE.md`, `adr/README.md`, and likely a durable ADR because the default git policy and session review controls change long-lived workflow behavior.
- External systems and secrets: session overlays may contain booleans, stage names, model IDs or aliases, effort levels, budgets, prompt profiles, and blocking policies only; they must not contain Claude credentials, tokens, private endpoints, or values from `.secrets.local.env`.
- Verification: run OpenSpec validation, schema validation if schema or prompt guidance changes, `scripts/check-overlay`, Claude review helper/session-overlay checks, git status checks, and tracked-secret checks before archive.
