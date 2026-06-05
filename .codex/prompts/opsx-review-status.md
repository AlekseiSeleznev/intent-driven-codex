---
description: Show effective OpenAI-compatible artifact review settings for this OpenSpec session
argument-hint: optional change name
---

Project constitution preflight:
- Read and enforce root `CONSTITUTION.md`; use the `project-constitution` skill rules; for architecture-sensitive work also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- Do not read `.secrets.local.env`; status is non-secret session/config inspection only.

Show artifact review session status:
1. Run `scripts/openspec-session-state --status`.
2. If `.codex/openspec-artifact-review.json` exists, validate ordinary session-effective config with `scripts/openspec-artifact-review --validate-config`; use explicit `--config <path>` for deterministic external files and `--raw-config` / `--ignore-session` for tracked raw config diagnostics.
3. Summarize session decision, git checkpoint mode, effective enabled/disabled state, provider base URL, API key variable name only, and per-stage model/effort/budget/blocking settings.
4. Omit secret values. Do not read `.secrets.local.env` and do not call the live provider.
5. No temporary effective config file is required for ordinary status; delete any diagnostic temporary files if you create them.
