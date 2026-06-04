---
description: Enable OpenAI-compatible artifact review for the current OpenSpec session
argument-hint: none
---

Project constitution preflight:
- Read and enforce root `CONSTITUTION.md`; use the `project-constitution` skill rules before changing review state.
- Do not read `.secrets.local.env`; these commands inspect or change non-secret session/config metadata only.

Enable artifact review for this session:
1. Run `scripts/openspec-session-state --review on`.
2. Show `scripts/openspec-session-state --status`.
3. Generate a temporary effective config with `scripts/openspec-session-state --effective-review-config --out <tmp>` and validate it with `scripts/openspec-artifact-review --validate-config --config <tmp>`.
4. Leave `.codex/openspec-artifact-review.json` unchanged unless the user explicitly asks to change global defaults.
5. Do not call the live provider; lifecycle commands run review later when configured.
