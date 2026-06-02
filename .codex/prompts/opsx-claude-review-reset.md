---
description: Clear Claude artifact review session overrides
argument-hint: none
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`.
- Do not read `.secrets.local.env`; this command changes local non-secret session state only.

Reset Claude artifact review session settings:
1. Run `scripts/openspec-session-state --claude-review reset`.
2. Run `scripts/openspec-session-state --status` and show that `claudeReview.decision` is `unset`.
3. Explain that later `/opsx:new`, `/opsx:propose`, `/opsx:ff`, or `/opsx:apply` should ask again before creating/consuming reviewable lifecycle artifacts.
4. Leave `.codex/openspec-claude-review.json` unchanged.
