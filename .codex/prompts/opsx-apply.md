---
description: Apply an OpenSpec change by implementing pending tasks
argument-hint: optional change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.


Claude review session intent:
- Before key entry workflows create or consume reviewable artifacts, run `scripts/openspec-session-state --status` when the helper exists.
- If `claudeReview.decision` is `unset`, ask whether to enable Claude artifact review for this session; record the answer with `scripts/openspec-session-state --claude-review on` or `--claude-review off`.
- Reuse the recorded session decision on later commands. Use `/opsx:claude-review-status`, `/opsx:claude-review-on`, `/opsx:claude-review-off`, `/opsx:claude-review-reset`, and `/opsx:claude-review-set` to inspect or change it.
- Session settings are non-secret local overlays over `.codex/openspec-claude-review.json`; do not edit global defaults unless the user explicitly asks.

Use skill `openspec-apply-change`.

Hard gate before implementation:
1. Run `git status --short`.
2. Run `openspec status --change <name> --json`.
3. Run `openspec instructions apply --change <name> --json`.
4. Refuse to apply if planning artifacts are missing or if `openspec/changes/<name>/{proposal.md,grill.md,design.md,design-review.md,adr.md,test-plan.md,tasks.md,specs/**}` has uncommitted changes, unless the user gives an explicit one-time override.
5. For intent-driven changes, read every file in `contextFiles`, any `openspec/changes/<name>/reviews/*.json` Claude artifact review reports as auxiliary context, and then read top-level `adr/*.md` when present. If `contextFiles.grill`, `contextFiles.design-review`, or `contextFiles.test-plan` are missing for an expanded intent-driven change, treat apply as blocked. If `contextFiles.adr` points at per-change `adr.md`, still derive durable constraints from top-level ADRs.

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

Остановись без завершения goal, если planning artifacts грязные, grill/design-review/test-plan отсутствуют или грязные, OpenSpec state blocked/all-done, отсутствуют credentials/secrets, недоступен внешний сервис, проверки падают по причинам вне контроля Codex, артефакты противоречат друг другу, требуется design/spec/ADR decision, либо нужен archive/merge/push/destructive git action или другое действие с отдельным approval. В отчете укажи blocker, trusted state, files changed so far и recommended next user action.
```


Claude Review reporting:
- If `/opsx:apply` invokes Claude artifact review during implementation, parse each helper JSON output or persisted report and include a user-visible `## Claude Review` block after every reviewer call; tool output alone is not sufficient.
- The `## Claude Review` block MUST include `Budget / cost`: `cost.total_cost_usd` when present, otherwise `стоимость недоступна`; the configured `maxBudgetUsd` / budget cap from `budgetLimitUsd` or the effective config, including `null` / `none`; and skipped/unavailable status such as `review не запускался, cost unavailable, budget cap: none`.
- The block MUST include `User-facing questions`: only questions that require user decision. If no such questions remain, write exactly `Вопросов, требующих участия пользователя: нет.` Resolve questions answerable from repository context/docs yourself and state the resolution briefly.
- The block MUST include `Short summary`: verdict, short summary of Claude's message, and what you did with findings: fixed and reran review; deferred to a concrete artifact/file/section; marked non-actionable with reason; or stopped on a blocker. Do not print every mustFix/shouldFix/question unless user participation is required.
- Before checkpointing, ensure every mustFix, actionable shouldFix, warning, and question is handled by fixing, deferring to a concrete place, marking non-actionable with reason, or asking the user as a blocker. Do not checkpoint while any Claude finding is unhandled.
- In the final apply summary, include `Total Claude review cost: $<value>` for Claude artifact reviewer calls. Use current-run tracked costs when available; otherwise, when persisted reports exist, run `scripts/openspec-claude-review --cost-summary --change <name>` and label the result as the persisted report total for the change. If review ran or reports exist but no numeric cost metadata exists, report `Total Claude review cost: unavailable`.

Implementation policy:
- Use the project-local `tdd` skill automatically for behavior-changing work. Work one vertical RED -> GREEN -> REFACTOR slice at a time: observe RED before production code, implement minimal GREEN, refactor only while green, and record evidence.
- Work through pending tasks in small groups.
- Mark each task complete only after RED/GREEN evidence or an approved test-plan exception.
- Pause on ambiguity, unresolved grill findings, missing RED/GREEN evidence, failing checks, or artifact mismatch.
- After each coherent task group, show diff/status and checkpoint according to session git mode; automatic mode may commit safe scoped apply-group checkpoints after paths/message/hash are shown.

Guardrails:
- If the user overrides the planning-artifact gate, state exactly which uncommitted files are being trusted and record that apply is proceeding under override.
- Do not archive from apply.
- Safe lifecycle/apply checkpoint commits are automatic by default in session git `auto` mode; do not merge, push, archive, run destructive git operations, or bypass hard gates unless explicitly approved.
