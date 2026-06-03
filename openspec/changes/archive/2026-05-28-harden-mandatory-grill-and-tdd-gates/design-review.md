## Context Read

- `proposal.md`, delta specs, `grill.md`, and `design.md` for this change.
- Existing v0.1.2 lifecycle artifacts and docs.
- Upstream Matt `grill-with-docs` and `tdd` skill references.
- Project constitution, architecture snapshot, and ADRs.

## Plan Summary

- The design makes `grill.md` pre-design and `design-review.md` post-design.
- `CONTEXT.md` is added as glossary-only context.
- Matt `tdd` becomes canonical for apply; Superpowers TDD remains optional supplemental behavior when present.
- `test-plan.md` becomes strict RED/GREEN/REFACTOR slice planning.

## Question Loop

### Q1: Does the design conflict with ADR 0001's project-local overlay decision?

Recommended answer: No. The change stays in project-local schema/prompts/skills/scripts/docs and does not patch OpenSpec CLI.

Resolution: No conflict.

### Q2: Does adding `CONTEXT.md` conflict with `CONSTITUTION.md` or `ARCHITECTURE.md`?

Recommended answer: No if responsibilities stay separate: glossary vs policy vs architecture snapshot.

Resolution: No conflict; docs and skills must state the boundary explicitly.

### Q3: Is a durable ADR needed?

Recommended answer: Yes. The lifecycle and canonical TDD choice are hard to reverse, non-obvious, and trade off compatibility against enforcement.

Resolution: Create ADR 0005.

## Artifact Updates Required

- `adr.md` must reference ADR 0005.
- `test-plan.md` must require RED/GREEN evidence for docs/scripts/smoke tests that validate the new behavior.
- `tasks.md` must include schema, skills, prompts, docs, checks, archive, publish, and global install work.

## Open Questions

None.
