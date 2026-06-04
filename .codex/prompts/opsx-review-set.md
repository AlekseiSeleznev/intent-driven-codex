---
description: Set OpenAI-compatible artifact review provider, model, effort, budget, or blocking for a session stage
argument-hint: --stage <stage> key=value ...
---

Project constitution preflight:
- Read and enforce root `CONSTITUTION.md`; use the `project-constitution` skill rules before changing review state.
- Do not read `.secrets.local.env`; these commands inspect or change non-secret session/config metadata only.

Set session-level artifact review stage settings:
1. Parse the requested stage and key/value settings. Supported keys include `model`, `effort`, `budget`/`maxBudgetUsd`, `budgetRub`/`maxBudgetRub`, `baseUrl`, `endpoint`, `apiKeyEnv`, `structuredOutputMode`, `blockOn`, `enabled`, and non-secret request routing keys such as `providerOnly`, `providerIgnore`, `providerOrder`, `providerSort`, `allowFallbacks`, `providerMaxPrice`, or JSON `requestProvider`.
2. Run `scripts/openspec-session-state --set-review-stage <stage> key=value ...`.
3. Generate a temporary effective config with `scripts/openspec-session-state --effective-review-config --out <tmp>` and validate it with `scripts/openspec-artifact-review --validate-config --config <tmp>`.
4. Show concise status and effective stage settings. Omit secret values; `apiKeyEnv` is allowed, real API keys are not.
5. Leave `.codex/openspec-artifact-review.json` unchanged unless the user explicitly asks to change global defaults.
