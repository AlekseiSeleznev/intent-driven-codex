## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — Accepted and in force; preserves the OpenSpec CLI/project-local overlay boundary.
- `adr/0003-formalize-project-context-entrypoints.md` — Accepted and in force; root project context and local-secret boundaries remain outside OpenSpec artifacts.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — Accepted and in force; lifecycle gates and TDD evidence remain mandatory.
- `adr/0006-adopt-claude-artifact-review.md` — Accepted and in force; README/docs must now include optional Claude artifact review accurately.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — Accepted and in force; README/docs must now include automatic safe checkpoints and session review controls accurately.

## Constitution / Architecture Rules Considered

- `CONSTITUTION.md` requires OpenSpec CLI 1.3.x-compatible public commands, project-local overlay files, Markdown OpenSpec artifacts, Bash scripts under `scripts/`, durable ADRs under `adr/`, and root `ARCHITECTURE.md` as the snapshot.
- Secret handling rules forbid reading or committing `.secrets.local.env`; this change does not need external systems or secrets.
- `ARCHITECTURE.md` states OpenSpec remains lifecycle engine; Codex overlay owns prompts/skills/helpers/docs.
- Archive, merge, push, destructive operations, and hard-gate bypasses remain explicit-approval operations.

## Decisions Evaluated

- Consolidate validation report findings F-01 through F-14 into one implementation change.
  - Accepted because the findings are one cohesive docs/config drift cleanup and include critical install fixes.
- Bump template version to `0.1.4` and add `CHANGELOG.md`.
  - Accepted because recently archived changes introduced public feature additions beyond a patch-level update.
- Add lightweight drift checks instead of a comprehensive docs semantics checker.
  - Accepted because the latter is brittle and outside this change's scope.
- Create a new durable ADR.
  - Rejected because no new architecture boundary or hard-to-reverse decision is introduced; this change aligns docs/config with existing ADRs.

## New Durable ADRs Created

- None.

## Superseded ADRs

- None.

## Architecture Snapshot Updates

- `ARCHITECTURE.md` will be edited only to fix existing ADR 0007 wording, not to change the architecture snapshot or in-force ADR set.

## No ADR Needed

- No durable ADR is needed because the work is documentation/config alignment with already accepted architecture decisions. The change-local artifacts and `CHANGELOG.md` are sufficient record of the release/documentation cleanup.
