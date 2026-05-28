## ADR Review

## Existing In-Force ADRs

- ADR 0001: project-local Codex/OpenSpec overlay; OpenSpec CLI remains unpatched.
- ADR 0003: root `CONSTITUTION.md`, root `ARCHITECTURE.md`, top-level `adr/`, and local-secret boundaries.
- ADR 0004: v0.1.2 mandatory `review.md` and `test-plan.md` gates.

## Constitution / Architecture Rules Considered

- `CONSTITUTION.md` requires project-local OpenSpec schema, Codex overlay prompts/skills, Bash helper scripts, top-level durable ADRs, and `ARCHITECTURE.md` as snapshot.
- No external systems or secrets are needed.
- `ARCHITECTURE.md` must be updated when an accepted durable ADR changes the current snapshot.

## Decisions Evaluated

- Whether to keep a single post-design `review.md` or split grill into pre-design and post-design gates.
- Whether to rely on Superpowers TDD or vendor Matt `tdd` as project-local canonical behavior.
- Whether `CONTEXT.md` should become policy/architecture or remain glossary-only.

## New Durable ADRs Created

- `adr/0005-adopt-matt-grill-and-tdd-gates.md`

## Superseded ADRs

- ADR 0005 supersedes ADR 0004 for lifecycle gate structure and TDD enforcement.
- ADR 0001 and ADR 0003 remain in force and are not superseded.

## Architecture Snapshot Updates

- Update `ARCHITECTURE.md` to list ADR 0005 as in-force and ADR 0004 as superseded.
- Update lifecycle text and diagrams to include `grill` and `design-review`.
- Update `adr/README.md` accordingly.

## No ADR Needed

Not applicable; a durable ADR is required.
