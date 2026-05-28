---
description: Apply an OpenSpec change by implementing pending tasks
argument-hint: optional change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-apply-change`.

Hard gate before implementation:
1. Run `git status --short`.
2. Run `openspec status --change <name> --json`.
3. Run `openspec instructions apply --change <name> --json`.
4. Refuse to apply if planning artifacts are missing or if `openspec/changes/<name>/{proposal.md,design.md,review.md,adr.md,test-plan.md,tasks.md,specs/**}` has uncommitted changes, unless the user gives an explicit one-time override.
5. For intent-driven changes, read every file in `contextFiles` and then read top-level `adr/*.md` when present. If `contextFiles.review` or `contextFiles.test-plan` are missing for an expanded intent-driven change, treat apply as blocked. If `contextFiles.adr` points at per-change `adr.md`, still derive durable constraints from top-level ADRs.

Goal-guidance preflight:
- Run this after apply eligibility is known and context is available, but before implementation file edits.
- If the current session is already inside an active Codex Goal whose objective covers `<name>`, state that nested goal generation is skipped and continue normal apply.
- If the user explicitly asks to continue without a goal, states "no goal", or otherwise clearly bypasses goal guidance, state the bypass and continue normal apply.
- Do not generate a goal prompt when apply is blocked, all-done, or stopped by dirty planning artifacts; report the blocker instead.
- Generate a copy-paste `/goal` prompt and stop before implementation when any condition applies:
  - three or more pending tasks remain;
  - the work is expected to cross multiple checkpoint boundaries;
  - material design or ADR constraints affect implementation;
  - multiple capabilities, subsystems, or documentation plus code are affected;
  - external services, credentials, generated assets, migrations, or long verification are needed;
  - the user asks Codex to keep going, implement everything, or avoid stopping except for blockers.
- Small, local work may continue without goal guidance if it is unlikely to need multiple checkpoints.

Generated apply goal shape:

```text
/goal Реализуй Intent-Driven OpenSpec change <change> в текущем проекте до состояния verify-ready.

Первое действие: запусти workflow `/opsx:apply <change>`. Если вложенная slash-команда не исполняется буквально, используй workflow/skill `openspec-apply-change` для этого change.

Критерии завершения: все применимые pending tasks выполнены и отмечены только после проверки; `/opsx:verify <change>` завершен без critical issues; итоговый отчет перечисляет выполненные задачи, измененные файлы, статус проверки и нерешенные предупреждения; необходимые checkpoint boundaries показаны пользователю.

Остановись без завершения goal, если planning artifacts грязные, review/test-plan отсутствуют или грязные, OpenSpec state blocked/all-done, отсутствуют credentials/secrets, недоступен внешний сервис, проверки падают по причинам вне контроля Codex, артефакты противоречат друг другу, требуется design/spec/ADR decision, либо нужен archive/merge/push/destructive git action или другое действие с отдельным approval. В отчете укажи blocker, trusted state, files changed so far и recommended next user action.
```

Implementation policy:
- Work through pending tasks in small groups.
- Mark each task complete only after verification.
- Pause on ambiguity, failing checks, or artifact mismatch.
- After each coherent task group, show diff/status and propose a checkpoint.

Guardrails:
- If the user overrides the planning-artifact gate, state exactly which uncommitted files are being trusted and record that apply is proceeding under override.
- Do not archive from apply.
- Do not merge/push/commit unless explicitly approved.
