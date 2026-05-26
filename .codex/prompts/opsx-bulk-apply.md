---
description: Apply multiple OpenSpec changes concurrently in isolated worktrees
argument-hint: optional change names
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-bulk-apply-change`.

Rules:
- Use only when two or more active changes are eligible.
- Run git discipline checks before creating worktrees.
- Run goal-guidance preflight after executable changes are known and before creating worktrees or dispatching subagents.
- Create one isolated worktree per executed change under `.worktrees/<change>` unless the user specifies another root.
- Dispatch one Codex subagent per worktree when subagents are available.
- Each subflow runs `/opsx:apply <change>` and `/opsx:verify <change>`.
- Report results without merging or archiving.

Goal-guidance preflight:
- If fewer than two executable changes remain, stop and suggest `/opsx:apply <change>`; do not generate a bulk goal.
- If the current session is already inside an active Codex Goal whose objective covers the same changes, state that parent goal generation is skipped and continue orchestration.
- If the user explicitly asks to continue without a goal, states "no goal", or otherwise clearly bypasses goal guidance, state the bypass and continue orchestration.
- Otherwise, for two or more executable changes, present one copy-paste parent `/goal` prompt and stop before creating worktrees or dispatching subagents.

Generated bulk goal shape:

```text
/goal Проведи Intent-Driven OpenSpec bulk apply для изменений <change-a>, <change-b>, ... в текущем проекте.

Первое действие: запусти workflow `/opsx:bulk-apply <change-a> <change-b> ...`. Если вложенная slash-команда не исполняется буквально, используй workflow/skill `openspec-bulk-apply-change` с теми же изменениями.

Критерии завершения: у каждого выполненного change есть isolated worktree, apply result, `/opsx:verify <change>` result, changed-files summary, blocker summary и normalized parent report; skipped/paused/failed changes имеют причины; parent report перечисляет worktree paths, changed files, blockers и verify status.

Остановись без завершения goal, если осталось меньше двух executable changes, planning-artifact gate не пройден, OpenSpec state blocked/all-done для нужного change, worktree creation failed, subagent dispatch failed, credentials/secrets или external services недоступны, checks fail вне контроля Codex, есть artifact contradictions, требуется design/spec/ADR decision, возник merge/worktree conflict, либо нужен archive/merge/push/destructive git action или другое действие с отдельным approval. Не выполняй merge, archive, push или destructive git actions без отдельного явного approval.
```

Guardrails:
- Do not apply in the parent workspace.
- Do not merge, archive, push, or commit without explicit follow-up approval.
- If fewer than two executable changes remain, stop and suggest `/opsx:apply <change>`.
