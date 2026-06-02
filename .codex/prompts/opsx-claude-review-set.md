---
description: Set Claude artifact review model, effort, budget, or blocking for a session stage
argument-hint: --stage <stage> key=value ...
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive stage settings, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- Do not read `.secrets.local.env`; this command changes local non-secret session state only.

Set session-level Claude review stage settings:
1. Parse the requested stage and settings. Supported settings map to `scripts/openspec-session-state --set-stage <stage> key=value ...`, including `model`, `effort`, `budget`/`maxBudgetUsd`, `promptProfile`, `blockOn`, and `enabled`.
2. Run `scripts/openspec-session-state --set-stage <stage> key=value ...`.
3. Generate a temporary effective config with `scripts/openspec-session-state --effective-claude-config --out <tmp>` and validate it with `scripts/openspec-claude-review --validate-config --config <tmp>`.
4. Show effective non-secret settings and any model/effort warnings. Explain when a model is a floating alias.
5. Leave `.codex/openspec-claude-review.json` unchanged unless the user explicitly asks to change global defaults.
