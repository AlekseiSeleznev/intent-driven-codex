---
description: Disable Claude artifact review for the current OpenSpec session
argument-hint: optional reason
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`.
- Do not read `.secrets.local.env`; this command changes local non-secret session state only.

Disable Claude artifact review for this session:
1. Run `scripts/openspec-session-state --claude-review off`.
2. Run `scripts/openspec-session-state --status` and show that the session decision is disabled.
3. Leave `.codex/openspec-claude-review.json` unchanged unless the user explicitly asks to change global defaults.
4. Do not remove historical review reports; they remain auxiliary context for the change that produced them.
