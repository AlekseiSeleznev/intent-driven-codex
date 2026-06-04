## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — Accepted and in force; OpenSpec remains the lifecycle/source-of-truth engine while Codex overlay owns prompts, skills, and workflow behavior.
- `adr/0003-formalize-project-context-entrypoints.md` — Accepted and in force; root context files and local secret boundaries remain outside OpenSpec artifacts.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — Accepted and in force; mandatory grill/design-review and TDD evidence remain required.
- `adr/0006-adopt-claude-artifact-review.md` — Accepted and in force; Claude review remains optional, structured, secret-safe, stage-configured, and helper/report based.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — Accepted and in force; session review controls, automatic safe checkpoints, and budget-exhaustion disablement remain active.

## Constitution / Architecture Rules Considered

- `CONSTITUTION.md` requires project-local overlay updates, public OpenSpec CLI compatibility, Bash helper scripts where used, Markdown OpenSpec artifacts, and no secret values in tracked files.
- `CONSTITUTION.md` requires `scripts/check-overlay` after overlay/template updates.
- `ARCHITECTURE.md` places Claude artifact review in the Codex overlay/helper layer, not OpenSpec CLI internals.
- `.secrets.local.env` is not needed and must not be read.
- Safe lifecycle checkpoints may run automatically; push/merge/destructive actions still require explicit approval.

## Decisions Evaluated

- Render the human `## Claude Review` block from prompts/skills rather than the helper.
  - Accepted because the helper should remain machine-readable and Codex owns conversation output.
- Add `budgetLimitUsd` to structured reports instead of requiring assistants to reread effective config after each review.
  - Accepted because it makes persisted reports sufficient for budget-cap display and is backward-compatible.
- Use deterministic text-marker checks in `scripts/check-overlay` for prompts/skills/docs.
  - Accepted because these files are Markdown guidance and the project already uses overlay drift checks for documentation/prompt behavior.
- Do not create a new durable ADR.
  - Accepted because this is a reversible refinement of existing Claude review and session-control behavior, not a new architecture boundary or hard-to-reverse trade-off.

## New Durable ADRs Created

- None.

## Superseded ADRs

- None.

## Architecture Snapshot Updates

- None required. `ARCHITECTURE.md` already describes Claude review helper/config/report/session boundaries. This change strengthens user-facing workflow output within those boundaries.

## No ADR Needed

- No durable ADR is needed because ADR 0006 and ADR 0007 already capture the stable architecture decisions. The current change is an overlay reporting and regression-check refinement that can be reverted without changing architectural structure.
