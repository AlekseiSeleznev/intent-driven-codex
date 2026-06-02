---
description: Show effective Claude artifact review settings for this OpenSpec session
argument-hint: optional change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- Do not read `.secrets.local.env`; Claude review status is non-secret session/config inspection only.

Show Claude artifact review session status:
1. Run `scripts/openspec-session-state --status`.
2. If `.codex/openspec-claude-review.json` exists, generate a temporary effective config with `scripts/openspec-session-state --effective-claude-config --out <tmp>` and validate it with `scripts/openspec-claude-review --validate-config --config <tmp>`.
3. Summarize the session decision, git checkpoint mode, effective enabled/disabled state, and per-stage model/effort/budget/blocking settings.
4. Omit secret values. Do not read `.secrets.local.env` and do not call live `claude -p`.
5. Delete temporary effective config files after use.
