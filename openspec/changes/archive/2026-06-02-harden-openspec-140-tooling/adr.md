## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — Accepted; remains in force. This change stays within the project-local overlay/helper-script model and does not patch OpenSpec.
- `adr/0003-formalize-project-context-entrypoints.md` — Accepted; remains in force. This change preserves root context and local-secret boundaries.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — Accepted; remains in force. This change follows grill/design-review and will use command-based TDD slices.
- `adr/0006-adopt-claude-artifact-review.md` — Accepted; remains in force but not materially affected.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — Accepted; remains in force. This change uses safe local checkpoint boundaries and does not broaden automatic git operations.
- Superseded ADRs 0002 and 0004 were considered only as historical context through their superseding ADRs.

## Constitution / Architecture Rules Considered

- `CONSTITUTION.md` requires OpenSpec public CLI usage, project-local overlay updates, `scripts/check-overlay` after overlay/template changes, no OpenSpec package patching, and no `.secrets.local.env` reads unless a listed external system is needed.
- `ARCHITECTURE.md` states OpenSpec owns lifecycle state while Codex overlay/helper scripts own workflow behavior and update checks.
- Secret values must not be printed, staged, copied, archived, or added to OpenSpec artifacts; this change uses only non-secret paths and local fake executables in tests.

## Decisions Evaluated

- Track auto-repair tooling in the source repository rather than patching only the installed global share. This is implementation of ADR 0001's existing source-controlled overlay model, not a new durable architecture decision.
- Keep installed documentation aliases while supporting canonical source docs in doctor/repair checks. This preserves Brownfield safety from the existing installer behavior.
- Use local path/version checks for stale real OpenSpec detection instead of package-manager/network checks. This is a verification design detail, not a durable architecture change.
- Add a focused regression script and call it from `scripts/check-overlay`; this follows existing update-safety verification rules.

## New Durable ADRs Created

- None.

## Superseded ADRs

- None.

## Architecture Snapshot Updates

- None. The current architecture snapshot already covers project-local helper scripts, OpenSpec public CLI delegation, update-safety checks, and no-secret boundaries.

## No ADR Needed

- No durable ADR is needed because the change implements existing accepted architecture: source-controlled project-local overlay tooling that delegates to OpenSpec and validates compatibility through local checks. No in-force ADR is superseded, and no current architecture snapshot behavior changes.
