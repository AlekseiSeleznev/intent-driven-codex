---
description: Create a new OpenSpec change and generate planning artifacts up to apply-ready state
argument-hint: change name or description
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.


Artifact review session intent:
- Before key entry workflows create or consume reviewable artifacts, run `scripts/openspec-session-state --status` when the helper exists.
- If `artifactReview.decision` is `unset`, ask whether to enable OpenAI-compatible artifact review for this session; record the answer with `scripts/openspec-session-state --review on` or `--review off`.
- Reuse the recorded session decision on later commands. Use `/opsx:review-status`, `/opsx:review-on`, `/opsx:review-off`, `/opsx:review-reset`, and `/opsx:review-set` to inspect or change it.
- Session settings are non-secret local overlays over `.codex/openspec-artifact-review.json`; do not edit global defaults unless the user explicitly asks.

Use skill `openspec-propose` to create a change and generate planning artifacts, including mandatory `grill.md`, `design-review.md`, and `test-plan.md`. For grill gates, run `grill-with-docs` automatically; do not use a plain review flow.

Intent-driven adaptation:
- Target lifecycle is `proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks`.
- Create `grill.md` after specs and before design using `grill-with-docs`. Create `design-review.md` after design using `grill-with-docs`. If no material questions remain, record that conclusion; otherwise integrate accepted grill resolutions before the next gate.
- Use `gherkin-authoring` guidance for specs.
- Use `c4-diagrams` guidance for non-trivial design boundaries.
- Use `architectural-decision-records` guidance for ADR review after `design-review.md`.
- Create `test-plan.md` after ADR using the project-local `tdd` skill to map requirements/scenarios to vertical RED/GREEN/REFACTOR slices before `tasks.md`.

Optional OpenAI-compatible artifact review:
- When configured, call `scripts/openspec-artifact-review --change <name> --stage <artifact>` after each generated artifact and before the next dependent artifact. Stop on blocking verdict/config/parse errors; checkpoint any tracked review report with the artifact.
- After each reviewer command, parse the helper JSON output or persisted report and include a user-visible `## Artifact Review` block; tool output alone is not sufficient.
- The `## Artifact Review` block MUST include `Budget / cost`: `cost.total_cost_usd` when present, otherwise `стоимость недоступна`; the configured `maxBudgetUsd` / budget cap from `budgetLimitUsd` or the effective config, including `null` / `none`; and skipped/unavailable status such as `review не запускался, cost unavailable, budget cap: none`.
- The block MUST include `User-facing questions`: only questions that require user decision. If no such questions remain, write exactly `Вопросов, требующих участия пользователя: нет.` Resolve questions answerable from repository context/docs yourself and state the resolution briefly.
- The block MUST include `Short summary`: verdict, short summary of the reviewer message, and what you did with findings: fixed and reran review; deferred to a concrete artifact/file/section; marked non-actionable with reason; or stopped on a blocker. Do not print every mustFix/shouldFix/question unless user participation is required.
- Before checkpoint/dependent artifact consumption, ensure every mustFix, actionable shouldFix, warning, and question is handled by fixing, deferring to a concrete place, marking non-actionable with reason, or asking the user as a blocker. Do not checkpoint while any artifact reviewer finding is unhandled.

Git discipline:
- Before starting, run `git branch --show-current` and `git status --short`; warn if not on `main` or if unrelated changes are present.
- After each artifact is written, run `openspec status --change <name> --json` and `git status --short`; show changed files and checkpoint according to session git mode before writing a dependent artifact.
- The normal workflow requires a checkpoint after each artifact. In session git `auto` mode, safe scoped checkpoint commits may run automatically after paths/message/hash are shown; manual mode stops for approval. Batch workflows must still show each checkpoint and final diff summary.

Guardrails:
- Do not implement code.
- Safe lifecycle checkpoint commits are automatic by default in session git `auto` mode; do not merge, push, archive, run destructive git operations, or bypass hard gates unless explicitly approved.
- Do not use external agent-runtime assumptions.
