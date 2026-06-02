## ADR Review

## Existing In-Force ADRs Considered

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — requires a project-local overlay and public OpenSpec CLI use rather than patching or forking OpenSpec.
- `adr/0003-formalize-project-context-entrypoints.md` — requires preserving `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/`, and local-secret boundaries as Codex-layer context.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — requires grill/design-review gates and TDD evidence for behavior-changing apply work.
- `adr/0006-adopt-claude-artifact-review.md` — relevant only to preserve existing review helper/config checks; this change does not alter Claude review architecture.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — establishes `opsx`, shim, session-state, and checkpoint helpers as Codex-overlay automation surfaces with scoped safety boundaries.

## Grill / Design-Review Findings Considered

- Installed projects should receive the auto-repair tooling that their overlay checker validates.
- Installed projects should keep installed documentation aliases; source canonical docs remain source-authored files.
- Parent Git-root diagnostics should be non-blocking, path-only, and secret-safe.
- Splitting source and installed check modes is not needed for this patch as long as the helper is layout-neutral.

## Decisions Evaluated

### Decision: Treat auto-repair tooling as template-owned installed overlay assets

Accepted for this change. `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, `scripts/test-auto-repair-tooling`, and `scripts/install-overlay` are template-owned distribution assets. Installing and repairing them makes installed-project checks match documented UX.

### Decision: Preserve installed doc aliases and adapt tests

Accepted for this change. Installed aliases avoid collisions with project-owned docs. Source canonical docs remain the authored source, and tooling maps canonical source docs to installed aliases.

### Decision: Add parent Git-root warnings

Accepted for this change. The warning improves operator understanding without changing Git ownership, staging, or repair behavior.

## New Durable ADRs Created

None.

## Superseded ADRs

None.

## Rationale for No Durable ADR

No new durable ADR is needed because the selected approach does not change the overlay architecture, lifecycle gates, checkpoint policy, project context model, secret handling, or OpenSpec boundary. It is a patch-level distribution and diagnostic refinement inside the architecture already accepted by ADR 0001, ADR 0003, ADR 0005, and ADR 0007.

## Architecture Snapshot Updates

No `ARCHITECTURE.md` update is required. The current architecture already includes auto-repair/session helpers as Codex-overlay assets and OpenSpec as an external lifecycle engine.
