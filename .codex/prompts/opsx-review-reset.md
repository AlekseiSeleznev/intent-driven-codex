---
description: Clear OpenAI-compatible artifact review session overrides
argument-hint: none
---

Project constitution preflight:
- Read and enforce root `CONSTITUTION.md`; use the `project-constitution` skill rules before changing review state.
- Do not read `.secrets.local.env`; these commands inspect or change non-secret session/config metadata only.

Reset artifact review session settings:
1. Run `scripts/openspec-session-state --review reset`.
2. Show `scripts/openspec-session-state --status`.
3. If `.codex/openspec-artifact-review.json` exists, validate the default effective config.
4. Leave `.codex/openspec-artifact-review.json` unchanged.
5. Do not call `scripts/openspec-artifact-review`; this command only changes non-secret review session state.
