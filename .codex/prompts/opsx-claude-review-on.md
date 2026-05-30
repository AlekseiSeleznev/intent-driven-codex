---
description: Enable Claude artifact review for the current OpenSpec session
argument-hint: optional stage/model/effort notes
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`.
- Do not read `.secrets.local.env`; this command changes local non-secret session state only.

Enable Claude artifact review for this session:
1. Run `scripts/openspec-session-state --claude-review on`.
2. Run `scripts/openspec-session-state --status` and show the non-secret session decision.
3. If review config exists, validate the effective config with `scripts/openspec-session-state --effective-claude-config --out <tmp>` and `scripts/openspec-claude-review --validate-config --config <tmp>`.
4. Leave `.codex/openspec-claude-review.json` unchanged unless the user explicitly asks to change global defaults.
5. Do not call live `claude -p`; lifecycle commands run review later when a configured stage requires it.
