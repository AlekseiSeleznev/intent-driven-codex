## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — Accepted and in force; OpenSpec remains the lifecycle engine while Codex overlay owns workflow behavior.
- `adr/0003-formalize-project-context-entrypoints.md` — Accepted and in force; root context files and local secret boundaries remain outside OpenSpec artifacts.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — Accepted and in force; mandatory grill/design-review and TDD evidence remain required.
- `adr/0006-adopt-claude-artifact-review.md` — Accepted and in force; Claude review remains optional, structured, secret-safe, and helper/config/report based.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — Accepted and in force; session review controls and automatic safe local checkpoints remain active.

## Constitution / Architecture Rules Considered

- `CONSTITUTION.md` requires OpenSpec CLI 1.3.x-compatible public commands, project-local Codex overlay files, Bash helper scripts, Markdown OpenSpec artifacts, durable ADRs under `adr/`, and no secret values in tracked files.
- `ARCHITECTURE.md` places Claude review orchestration in `scripts/openspec-claude-review` and prompt/skill guidance, not in the OpenSpec CLI.
- Local `.secrets.local.env` is not needed and must not be read.
- Push, merge, archive, destructive operations, and hard-gate bypasses still require explicit approval; safe local checkpoints may run automatically.

## Decisions Evaluated

- Preserve per-review cost under the existing report `cost` object instead of creating a new report schema version.
  - Accepted because it is backward-compatible and already matches existing helper output for real Claude calls.
- Add `--cost-summary --change <change>` to aggregate persisted review report costs.
  - Accepted because it gives `/opsx:apply` a deterministic no-Claude-call way to summarize cost from durable auxiliary review evidence.
- Do not query account remaining budget or provider billing APIs.
  - Rejected as scope because Claude Code does not expose a stable remaining-budget field in review output, and external billing access would introduce secrets/external-system behavior not needed for this request.
- Do not create a new durable ADR.
  - Accepted because this is a reporting refinement inside ADR 0006/0007 boundaries, not a new architecture boundary or hard-to-reverse trade-off.

## New Durable ADRs Created

- None.

## Superseded ADRs

- None.

## Architecture Snapshot Updates

- None required. The current snapshot already describes Claude review reports, budget exhaustion, session controls, and Codex-overlay ownership; this change only adds cost reporting behavior within those boundaries.

## No ADR Needed

- No durable ADR is needed because the change is incremental, reversible, and already covered by ADR 0006's structured Claude review reports and ADR 0007's session review controls. The change-local artifacts are sufficient to document helper and workflow reporting behavior.
