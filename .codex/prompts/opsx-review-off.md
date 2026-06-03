---
description: Disable OpenAI-compatible artifact review for the current OpenSpec session
argument-hint: none
---

Project constitution preflight:
- Read and enforce root `CONSTITUTION.md`; use the `project-constitution` skill rules before changing review state.
- Do not read `.secrets.local.env`; these commands inspect or change non-secret session/config metadata only.

Disable artifact review for this session:
1. Run `scripts/openspec-session-state --review off`.
2. Show `scripts/openspec-session-state --status`.
3. Leave `.codex/openspec-artifact-review.json` unchanged unless the user explicitly asks to change global defaults.
4. Do not call `scripts/openspec-artifact-review`; this command only changes non-secret review session state.
