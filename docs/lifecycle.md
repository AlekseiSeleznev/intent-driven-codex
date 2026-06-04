# Intent-driven lifecycle

Intent-Driven Codex uses OpenSpec for lifecycle state and Codex for context,
grill, TDD, verification, and Git discipline.

## Planning order

| Artifact | File | Depends on | Purpose |
| --- | --- | --- | --- |
| proposal | `openspec/changes/<change>/proposal.md` | — | Intent, scope, capabilities, impact. |
| specs | `openspec/changes/<change>/specs/**/spec.md` | proposal | Observable OpenSpec requirements and scenarios. |
| grill | `openspec/changes/<change>/grill.md` | proposal, specs | Mandatory Matt `grill-with-docs` pre-design interrogation. |
| design | `openspec/changes/<change>/design.md` | proposal, specs, grill | Technical approach after uncertainty is resolved. |
| design-review | `openspec/changes/<change>/design-review.md` | design | Mandatory Matt `grill-with-docs` post-design interrogation. |
| adr | `openspec/changes/<change>/adr.md` | design-review | Per-change ADR review and durable ADR references. |
| test-plan | `openspec/changes/<change>/test-plan.md` | adr | Matt TDD vertical RED/GREEN/REFACTOR slice plan. |
| tasks | `openspec/changes/<change>/tasks.md` | specs, grill, design, design-review, adr, test-plan | Dependency-ordered implementation checklist. |

Executable phases continue with:

```text
apply -> verify -> archive
```

## Grill gates

The `grill` and `design-review` artifacts use the vendored Matt
`grill-with-docs` skill.

Codex must:

1. read repository context before asking;
2. ask one material question at a time only when context cannot answer it;
3. include a recommended answer and rationale;
4. update `CONTEXT.md`, OpenSpec artifacts, or ADR candidates as decisions
   crystallize;
5. stop before the next gate unless `Open Questions` is `None` or a one-time
   override is recorded.

`CONTEXT.md` is glossary/domain language only. Project policy remains in
`CONSTITUTION.md`; current architecture remains in `ARCHITECTURE.md`; durable
rationale remains in `adr/`.

## Optional OpenAI-compatible artifact review

The lifecycle can run an additional OpenAI-compatible provider reviewer after selected artifacts when `.codex/openspec-artifact-review.json` is enabled. The reviewer is invoked through `scripts/openspec-artifact-review --change <change> --stage <artifact>`, uses configured model/effort values, optional request-provider routing, plus an optional budget cap, and writes structured auxiliary reports under `openspec/changes/<change>/reviews/` and appends live provider-call cost entries to `openspec/changes/<change>/reviews/artifact-review-costs.jsonl`. Budget caps are disabled by default (`maxBudgetUsd: null`) and are sent to `OpenAI-compatible chat completions` only when explicitly configured.

If OpenAI-compatible provider reports that the configured budget was exhausted (`error_max_budget_usd` / maximum-budget result), the helper returns a blocking review result and automatically disables artifact review for the current session through `.codex/session/openspec-session.json`. The user must raise or clear the cap and explicitly re-enable review before retrying.

After every reviewer invocation, the assistant must render a compact localized
user-facing status block; raw helper JSON or terminal output is not enough. The
block is wrapped between `---` separator lines, uses the current chat language,
and contains only review status, cost/budget, and aggregate finding disposition
counts unless user participation is required. Russian example:

```text
---
Ревью артефакта — test-plan: пройдено.
Стоимость: 0,82 ₽, всего по изменению: 1,31 ₽ / 3 вызова, бюджет: без лимита.
Замечания: критичных 0, желательных 0, предупреждений 0, вопросов пользователю 0 — всё закрыто.
---
```

Displayed money amounts round upward to the currency minor unit; RUB values
round upward to kopecks while raw JSON cost precision is preserved. The helper
cost summary includes the invocation count so retries and re-runs are counted,
not just the latest persisted stage report.

Detailed findings are not printed by default. The assistant records or verifies
a formal disposition for every must-fix, actionable should-fix, warning, and
question: fixed, deferred to a concrete artifact/file/section/task, marked
non-actionable with a reason, or stopped as user-blocked.

Before any checkpoint that includes a artifact review report, every must-fix,
actionable should-fix, warning, and question must be handled through one of
those dispositions. Unhandled artifact reviewer findings block the checkpoint: Do not checkpoint while any artifact reviewer finding is unhandled.

This reviewer is optional and never replaces mandatory OpenSpec artifacts, `grill-with-docs`, ADR review, Matt TDD, OpenSpec validation, or git checkpoints. Automated checks use `--validate-config`, `--dry-run`, and fixtures; live `OpenAI-compatible chat completions` calls require local auth and explicit project opt-in.

## TDD gate

The `test-plan` artifact uses the vendored Matt `tdd` skill. It defines vertical
slices:

```text
RED -> GREEN -> REFACTOR
```

Codex must not implement behavior-changing production code before observing the
corresponding failing test/check, unless `test-plan.md` records an explicit
pre-approved exception. Tests/checks should exercise public interfaces and
observable behavior rather than implementation details.

## Apply and verify

`/opsx:apply` reads all OpenSpec context files plus relevant root context and
then works one TDD slice at a time. A task is marked done only after RED/GREEN
evidence or an approved exception exists.

`/opsx:verify` checks task completion, requirement coverage, design adherence,
grill/design-review resolution integration, ADR compliance, and TDD evidence.
Missing RED/GREEN evidence for behavior-changing work is critical.

## Support skills

| Skill | Trigger | Rule |
| --- | --- | --- |
| `project-constitution` | before `/opsx:*` and lifecycle skills | read/enforce `CONSTITUTION.md`; use `CONTEXT.md`/`CONTEXT-MAP.md`, `ARCHITECTURE.md`, and ADRs when relevant |
| `grill-with-docs` | `grill.md`, `design-review.md` | automatic at gate; one material question at a time; update glossary/docs inline |
| `tdd` | `/opsx:apply` behavior changes, `test-plan.md` | automatic; vertical RED/GREEN/REFACTOR slices |
| `gherkin-authoring` | specs | observable OpenSpec scenarios |
| `c4-diagrams` | non-trivial boundaries | use Mermaid/ASCII diagrams or explicit no-diagram rationale |
| `architectural-decision-records` | ADR gate | durable ADRs in top-level `adr/`, append-only supersession |
| `openspec-git-discipline` | lifecycle + git history | checkpoints after artifacts and apply groups |


## Automatic checkpoint mode

Safe scaffold, artifact, review-report, and coherent apply-group checkpoints are automatic by default when session git discipline is `auto`. Codex must still show affected paths, commit message, resulting hash, and post-commit status. In automatic mode, `scripts/openspec-git-checkpoint` provides scoped checkpoint commits and fails closed on unrelated dirty paths. Manual mode can require approval before local checkpoint commits. Push, merge, pull request creation, archive, destructive Git operations, dirty unrelated work, and hard-gate bypasses still require explicit approval.

OpenAI-compatible artifact review can also be controlled per session through ignored non-secret `.codex/session/openspec-session.json`, managed by `scripts/openspec-session-state` and the `/opsx:review-*` prompts.
