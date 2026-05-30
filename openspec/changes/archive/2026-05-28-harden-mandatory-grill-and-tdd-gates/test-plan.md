## TDD Strategy

Use behavior-focused verification through public interfaces for this overlay. The implementation is primarily schema, docs, prompts, and scripts, so the RED/GREEN evidence is command-based and smoke-test based rather than unit-test-only.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| Schema artifact graph | `openspec status --change <smoke> --json` exposes hardened lifecycle | Before schema update, checker expecting `grill` and `design-review` fails | `scripts/check-overlay --no-smoke` and smoke path expect/pass new artifact list | Keep schema readable and OpenSpec-compatible |
| Apply context | `openspec instructions apply --change <smoke> --json` includes all hardened artifacts | Before template/checker update, apply context lacks `grill.md`/`design-review.md` | `scripts/check-overlay` validates context files | Avoid brittle JSON parsing beyond required IDs |
| TDD gate | `/opsx:apply` and skill text require Matt TDD RED/GREEN slices | Text search/check fails before prompt/skill updates | `scripts/check-overlay` plus grep checks pass | Keep instructions concise but mandatory |
| Documentation | README/architecture/specs describe v0.1.3 lifecycle | Version/lifecycle grep fails before docs update | Version and lifecycle grep checks pass | Avoid contradictory legacy `review.md` wording outside archive history |

## Mocking / Boundary Policy

- No mocks are needed for this repository change.
- OpenSpec CLI and shell scripts are tested through command execution.
- GitHub publishing and global installation are verified after local checks, not mocked in docs.

## Required Checks

- `openspec schema validate intent-driven`
- `openspec validate harden-mandatory-grill-and-tdd-gates --type change --strict` before archive
- `openspec validate --all --strict`
- `scripts/check-overlay`
- text checks for `v0.1.3`, hardened lifecycle, vendored `grill-with-docs`, and vendored `tdd`
- `git status --short` with no tracked secret files

## Evidence Log

- RED evidence: the pre-change v0.1.2 checker/schema expected `proposal/specs/design/review/adr/test-plan/tasks`; the hardened v0.1.3 test-plan expected `grill` and `design-review`, so the old checker/schema would fail the new artifact graph.
- GREEN evidence: `openspec schema validate intent-driven` passed after the schema update.
- GREEN evidence: `openspec validate harden-mandatory-grill-and-tdd-gates --type change --strict` passed.
- GREEN evidence: `scripts/check-overlay` passed and verified apply context includes `proposal/specs/grill/design/design-review/adr/test-plan/tasks`.
- GREEN evidence: `openspec status --change harden-mandatory-grill-and-tdd-gates --json` reports every planning artifact done.

## TDD Exceptions

None.
