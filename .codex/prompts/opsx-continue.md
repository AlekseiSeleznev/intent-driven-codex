---
description: Continue an OpenSpec change by creating exactly one ready artifact
argument-hint: optional change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-continue-change` to create the next ready artifact.

Intent-driven policy:
- Create exactly one artifact per invocation.
- Follow OpenSpec status and instructions.
- If multiple artifacts are ready, prefer the schema order from `openspec status --change <name> --json` and the intent-driven lifecycle: `proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks`.
- Read dependency files before creating the artifact.



Claude review session intent:
- Reuse the current session decision from `scripts/openspec-session-state --status` when the helper exists; `/opsx:continue` should not ask the same enablement question repeatedly by default.
- If no decision exists and the next ready artifact stage would run Claude review after resolving the effective config, ask once and record the answer with `scripts/openspec-session-state --claude-review on|off`.
- When review is enabled, generate a temporary effective config with `scripts/openspec-session-state --effective-claude-config --out <tmp>` and pass it to `scripts/openspec-claude-review --config <tmp>`.

Artifact support skills:
- For `specs`, recommend `gherkin-authoring` and preserve OpenSpec Markdown as the source of truth.
- For `grill`, run `grill-with-docs` automatically; do not ask whether to run it. Ask one material question at a time only when repository context cannot resolve it.
- For `design`, recommend `c4-diagrams` when boundaries/integrations are non-trivial; otherwise record an explicit no-diagram rationale.
- For `design-review`, run `grill-with-docs` automatically against the completed design; block ADR/test-plan/tasks if material questions remain.
- For `adr`, recommend `architectural-decision-records`, read `grill.md` and `design-review.md`, and preserve append-only durable ADR supersession rules.
- For `test-plan`, use the project-local `tdd` skill to define vertical RED/GREEN/REFACTOR slices before tasks.

Optional Claude artifact review:
- If `scripts/openspec-claude-review` and `.codex/openspec-claude-review.json` exist, run `scripts/openspec-claude-review --change <name> --stage <artifact> --config <tmp-effective-config>` after writing the artifact and before checkpoint/dependent artifact consumption. Exit `0` continues; exit `3` is a configured skip/unavailable policy; exits `1`/`2` block unless explicitly overridden.
- After each reviewer command, parse the helper JSON output or persisted report and include a user-visible `## Claude Review` block; tool output alone is not sufficient.
- The `## Claude Review` block MUST include `Budget / cost`: `cost.total_cost_usd` when present, otherwise `стоимость недоступна`; the configured `maxBudgetUsd` / budget cap from `budgetLimitUsd` or the effective config, including `null` / `none`; and skipped/unavailable status such as `review не запускался, cost unavailable, budget cap: none`.
- The block MUST include `User-facing questions`: only questions that require user decision. If no such questions remain, write exactly `Вопросов, требующих участия пользователя: нет.` Resolve questions answerable from repository context/docs yourself and state the resolution briefly.
- The block MUST include `Short summary`: verdict, short summary of Claude's message, and what you did with findings: fixed and reran review; deferred to a concrete artifact/file/section; marked non-actionable with reason; or stopped on a blocker. Do not print every mustFix/shouldFix/question unless user participation is required.
- Before checkpoint/dependent artifact consumption, ensure every mustFix, actionable shouldFix, warning, and question is handled by fixing, deferring to a concrete place, marking non-actionable with reason, or asking the user as a blocker. Do not checkpoint while any Claude finding is unhandled.
- Do not read `.secrets.local.env` for ordinary artifact review.

Git discipline:
- Before writing, identify the next artifact and verify dependency artifacts are already committed or explicitly covered by the user override.
1. Before creating a dependent artifact, run `git status --short`.
2. If prior artifact changes are uncommitted, checkpoint according to session git mode unless the user explicitly overrides the gate.
3. After writing the artifact, run `openspec status --change <name>` and `git status --short`.
4. Create or propose the checkpoint according to session git mode and stop before the next artifact.

Guardrails:
- Do not skip artifacts.
- Do not create more than one artifact.
- Safe lifecycle checkpoint commits are automatic by default in session git `auto` mode; do not merge, push, archive, run destructive git operations, or bypass hard gates unless the user explicitly approves.
