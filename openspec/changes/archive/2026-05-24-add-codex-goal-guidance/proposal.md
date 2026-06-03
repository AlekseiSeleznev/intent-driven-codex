## Why

Long-running `/opsx:apply` and `/opsx:bulk-apply` runs can lose their operational objective across checkpoints, pauses, subagents, and verification boundaries. The workflow needs project-specific Codex Goal guidance that lets the user start the right apply workflow with one copy-paste `/goal` message containing the exact target, completion criteria, and safe stop conditions.

## What Changes

- Add goal-guidance behavior to `/opsx:apply` and `openspec-apply-change` so large or risky apply runs can stop before implementation and generate a ready-to-copy `/goal ...` prompt.
- Add goal-guidance behavior to `/opsx:bulk-apply` and `openspec-bulk-apply-change` so bulk runs generate a parent `/goal ...` prompt that names the target changes and embeds the bulk-apply workflow.
- Define when goal guidance is required, recommended, skipped, or suppressed because the current session is already inside an active goal or the user explicitly requested no goal.
- Require generated goal prompts to include:
  - the concrete `/opsx:apply <change>` or `/opsx:bulk-apply <changes...>` workflow to run;
  - a semantic fallback to the corresponding skill if nested slash-command execution is not literal;
  - completion criteria that include apply, verify, final reporting, and checkpoint boundaries;
  - explicit stop-without-completion conditions for blocked OpenSpec state, dirty planning artifacts, missing credentials, unavailable external services, unresolved ambiguity, failing checks outside Codex control, merge/worktree conflicts, or destructive actions needing approval.
- Update installed documentation and agent instructions so users understand when to copy the generated `/goal` prompt and when normal `/opsx:*` execution proceeds without goal guidance.
- No **BREAKING** behavior: existing `/opsx:apply` and `/opsx:bulk-apply` remain available; goal guidance adds a preflight recommendation/hand-off path for long or risky runs.

## Capabilities

### New Capabilities

- None. This change extends existing Codex OpenSpec workflow behavior rather than introducing a separate capability area.

### Modified Capabilities

- `codex-opsx-workflow`: `/opsx:apply` and `/opsx:bulk-apply` gain generated copy-paste Codex Goal prompts with concrete workflow invocation, completion criteria, and stop conditions.
- `template-installation`: installed documentation explains goal-guided apply and bulk-apply usage, including the generated prompt pattern and when the user should continue without a goal.

## Impact

- Codex prompt files:
  - `.codex/prompts/opsx-apply.md`
  - `.codex/prompts/opsx-bulk-apply.md`
- Codex skills:
  - `.codex/skills/openspec-apply-change/SKILL.md`
  - `.codex/skills/openspec-bulk-apply-change/SKILL.md`
- Canonical specs:
  - `openspec/specs/codex-opsx-workflow/spec.md`
  - `openspec/specs/template-installation/spec.md`
- User and agent documentation:
  - `README.md`
  - `README.ru.md`
  - `AGENTS.md`
  - `docs/lifecycle.md`
- Global Codex installation must be refreshed after implementation so `/home/as/.codex/prompts` and `/home/as/.codex/skills` receive the updated behavior.
