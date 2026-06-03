## Context Read

- `AGENTS.md`, `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/README.md`, and in-force ADRs 0001, 0003, 0005, 0006, 0007.
- Existing specs `openspec/specs/template-installation/spec.md` and `openspec/specs/overlay-update-safety/spec.md`.
- Current implementation surfaces: `scripts/install-overlay`, `scripts/check-overlay`, `scripts/test-auto-repair-tooling`, `bin/opsx`, `bin/openspec-shim`, and `manifest.yaml`.
- User report from v0.1.5 installation: manual copies were needed for `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, `scripts/test-auto-repair-tooling`, and canonical docs.

## Plan Summary

The change should make installed projects self-checking after documented install/update/repair. The preferred path is to repair or install the missing auto-repair tooling through `scripts/install-overlay` and `manifest.yaml`, while keeping canonical docs as source-authored files and installed doc aliases as target-project files. `scripts/check-overlay` should retain source regression coverage in the source repository but avoid failing installed projects on source-only layout assumptions.

## Question Loop

### Question 1: Should installed projects receive auto-repair tooling or should `scripts/check-overlay` stop requiring it?

**Recommended answer:** Installed projects should receive `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, and any helper that `scripts/check-overlay --no-smoke` executes; otherwise the checker should explicitly classify the helper as source-only. This keeps the UX simple: a repaired installed project can run the checker it was given.

**Resolution from repository evidence:** `scripts/check-overlay` currently fails if the auto-repair executables and manifest are absent, and then executes `scripts/test-auto-repair-tooling all`. Because the installed-project success criterion includes running `scripts/check-overlay --no-smoke`, the installed path must either install that helper or make the checker skip source-only helper execution. The spec now permits both, but the implementation should prefer installing the helper when the checker uses it.

### Question 2: Should installed projects receive canonical docs or alias docs?

**Recommended answer:** Keep installed alias docs as the target-project layout and teach repair/check tooling to use canonical docs as source inputs. Do not require `docs/lifecycle.md` and `docs/update-safety.md` in installed projects.

**Resolution from repository evidence:** `scripts/install-overlay` intentionally copies `docs/lifecycle.md` to `docs/intent-driven-lifecycle.md` and `docs/update-safety.md` to `docs/intent-driven-update-safety.md`; `bin/opsx` already has `DOC_SOURCE_ALIASES`; current helper coverage should be strengthened around this behavior rather than reversing the layout.

### Question 3: Is a parent Git worktree warning necessary or should tooling silently prefer the target directory?

**Recommended answer:** Add a non-blocking warning. Some current tooling already tries to prefer the current directory when it looks like an overlay root, but users installing into directories under `/home/as` can still be confused when Git checks resolve to a parent worktree.

**Resolution from repository evidence:** `scripts/check-overlay` has a PWD-root heuristic for overlay roots, but `scripts/install-overlay` and `bin/opsx` do not currently explain nested parent Git roots. A warning is low-risk, non-secret, and helps users interpret status/ignore diagnostics.

## Resolved Terms

Updated `CONTEXT.md` with glossary entries for Source repository, Installed project, Auto-repair tooling, Canonical docs, and Installed doc aliases.

## Document Updates Applied

- Added OpenSpec delta specs for installed-project self-checking, auto-repair tooling seeding, source-vs-installed check boundaries, doc layout compatibility, and parent Git diagnostics.
- Updated glossary terms in `CONTEXT.md`.

## Document Updates Required Before Next Gate

- `design.md` must choose the exact implementation split between installing source regression helpers and skipping source-only checks.
- `design.md` must define where parent Git warnings are emitted and how they avoid leaking secret values.
- `test-plan.md` must include fresh installed, brownfield alias-doc, source no-smoke/full, no-op help, doctor, and stale-real-OpenSpec checks.

## ADR Candidates

- No new durable ADR is currently required. The change preserves ADR 0001's project-local overlay and public OpenSpec CLI boundary, ADR 0003's context/secret boundaries, ADR 0005's TDD/verification discipline, and ADR 0007's auto-repair/session-control architecture. The implementation choice is a distribution/diagnostic refinement rather than a new architecture commitment.

## Open Questions

None.
