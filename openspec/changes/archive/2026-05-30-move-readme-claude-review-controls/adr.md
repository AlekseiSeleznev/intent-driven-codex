## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — Accepted and in force; no architecture change.
- `adr/0003-formalize-project-context-entrypoints.md` — Accepted and in force; README remains public documentation, no secret changes.
- `adr/0006-adopt-claude-artifact-review.md` — Accepted and in force; Claude review behavior is unchanged.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — Accepted and in force; session controls are only documented in a better location.

## Constitution / Architecture Rules Considered

- `CONSTITUTION.md` requires no secret values in tracked files and standard validation for docs/template updates.
- No external systems or `.secrets.local.env` access are needed.

## Decisions Evaluated

- Move README session controls near optional Claude review documentation.
  - Accepted as a documentation organization change.
- Create a durable ADR.
  - Rejected because this is not an architectural decision or hard-to-reverse trade-off.

## New Durable ADRs Created

- None.

## Superseded ADRs

- None.

## Architecture Snapshot Updates

- None.

## No ADR Needed

- The change only relocates README sections and does not alter architecture, runtime behavior, or durable policy.
