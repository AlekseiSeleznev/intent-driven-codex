# Architectural Decision Records

This directory stores durable, project-level ADRs created by intent-driven OpenSpec changes.

## Current state

The current in-force durable ADRs are:

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — adopts the project-local Codex/OpenSpec overlay architecture for Intent-Driven Development.
- `adr/0003-formalize-project-context-entrypoints.md` — formalizes root `CONSTITUTION.md`, root `ARCHITECTURE.md`, the OpenSpec bridge, ADR-derived architecture snapshots, and local-secret boundaries.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — hardens the lifecycle with Matt `grill-with-docs` gates, root/context-local glossary support, and canonical Matt TDD apply discipline.

Superseded durable ADRs are:

- `adr/0002-adopt-project-constitution-preflight.md` — superseded by ADR 0003; its Codex-layer preflight and local-secret boundary are carried forward under the `CONSTITUTION.md`/`ARCHITECTURE.md` context model.
- `adr/0004-adopt-mandatory-review-and-test-plan-gates.md` — superseded by ADR 0005; its mandatory gate intent is carried forward as `grill.md`, `design-review.md`, and strict TDD evidence.

Target projects that install this template should create their own durable ADRs only when a concrete architectural decision needs to survive beyond a single change.

## Relationship to OpenSpec changes

Every intent-driven change gets mandatory per-change grill, ADR review, and test-plan files:

```text
openspec/changes/<change>/grill.md
openspec/changes/<change>/design-review.md
openspec/changes/<change>/adr.md
openspec/changes/<change>/test-plan.md
```

Those files are the required review/decision/verification gates. `grill.md` records pre-design Matt `grill-with-docs` findings, `design-review.md` records post-design Matt `grill-with-docs` findings, `adr.md` records architectural decision review, and `test-plan.md` records vertical Matt TDD slices and evidence expectations. The ADR review must record:

- existing in-force ADRs considered;
- grill/design-review findings considered;
- decisions evaluated by the change;
- any new durable ADR files created here;
- any older ADRs superseded by new ADRs;
- a short rationale when no durable ADR is needed.

## Durable ADR rules

- Durable ADRs live in this directory as `NNNN-kebab-title.md`.
- Accepted ADRs are append-only: do not rewrite history to change an accepted decision.
- To replace a decision, create a new ADR and mark the old one as superseded.
- Derive the in-force ADR set by reading all ADRs and following supersession links.
- Implementation and verification must obey the in-force ADR set unless a new change explicitly supersedes it.
