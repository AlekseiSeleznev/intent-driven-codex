---
description: Fast-forward OpenSpec planning artifacts with visible checkpoint policy
argument-hint: change name or description
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Fast-forward is a convenience wrapper around creating all planning artifacts needed before apply.

Use only when the user explicitly wants fast-forward behavior.

Rules:
- Use the repository default schema unless the user requests a schema.
- For `intent-driven`, generate artifacts in order: `proposal`, `specs`, `grill`, `design`, `design-review`, `adr`, `test-plan`, `tasks`.
- Follow `openspec instructions <artifact> --change <name> --json` for every artifact.
- Read dependencies before writing each artifact.
- Run `grill-with-docs` automatically for the mandatory `grill` and `design-review` artifacts; use `gherkin-authoring`, `c4-diagrams`, `architectural-decision-records`, and the project-local `tdd` skill when their artifacts require them.

Optional OpenAI-compatible artifact review:
- When configured, call `scripts/openspec-artifact-review --change <name> --stage <artifact>` after each generated artifact and before the next dependent artifact. Stop on blocking verdict/config/parse errors; checkpoint any tracked review report with the artifact.
- After each reviewer command, parse the helper JSON output or persisted report and include a compact localized user-visible review status block; tool output alone is not sufficient. Run `scripts/openspec-artifact-review --cost-summary --change "<name>"` after reviewer calls when persisted reports/ledger may exist; totals must include retries/re-runs, not just the latest stage report.
- The status block MUST be fully localized to the current chat language and wrapped between `---` separator lines. In Russian, use this compact shape: `Ревью артефакта — <stage>: <статус>.`, `Стоимость: <текущий вызов>, всего по изменению: <итого> / <N вызовов>, бюджет: <лимит>.`, `Замечания: критичных <N>, желательных <N>, предупреждений <N>, вопросов пользователю <N> — <итог>.` Do not use raw user-facing labels such as `Artifact Review`, `Findings`, `mustFix`, or `shouldFix` when the chat language is Russian.
- Round displayed monetary amounts upward to the currency minor unit; for RUB, round upward to kopecks and display with the chat-language decimal convention, e.g. `0.81953034 ₽` -> `0,82 ₽`. Preserve raw cost precision in JSON reports.
- Record or verify a formal finding disposition for every mustFix, actionable shouldFix, warning, and question: `fixed`, `deferred` to a concrete artifact/file/section/task, `non_actionable` with reason, or `user_blocked`. Print individual findings only when user participation is required.
- Before checkpoint/dependent artifact consumption, ensure every mustFix, actionable shouldFix, warning, and question is handled by fixing, deferring to a concrete place, marking non-actionable with reason, or asking the user as a blocker. Do not checkpoint while any artifact reviewer finding is unhandled.

Git discipline:
- Before the first artifact and after every artifact, run `git status --short` and show the checkpoint boundary.
- In session git `auto` mode, safe scoped artifact checkpoint commits may run automatically after paths/message/hash are shown.
- Mandatory checkpoints still apply and must be visible in the final summary.
- In manual mode, stop after the first artifact and recommend `/opsx:continue` if checkpoint approval is not available.
- Never hide commits, merges, pushes, or archive actions.


Artifact review session intent:
- Before key entry workflows create or consume reviewable artifacts, run `scripts/openspec-session-state --status` when the helper exists.
- If `artifactReview.decision` is `unset`, ask whether to enable OpenAI-compatible artifact review for this session; record the answer with `scripts/openspec-session-state --review on` or `--review off`.
- Reuse the recorded session decision on later commands. Use `/opsx:review-status`, `/opsx:review-on`, `/opsx:review-off`, `/opsx:review-reset`, and `/opsx:review-set` to inspect or change it.
- Session settings are non-secret local overlays over `.codex/openspec-artifact-review.json`; do not edit global defaults unless the user explicitly asks.
