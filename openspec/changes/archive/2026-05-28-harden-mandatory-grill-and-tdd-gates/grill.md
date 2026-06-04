## Context Read

- User request to harden the v0.1.2 gates with the full Matt Pocock `grill-with-docs` and `tdd` skills.
- Upstream Matt Pocock skill docs for `grill-with-docs` and `tdd`.
- Existing project context: `CONSTITUTION.md`, `ARCHITECTURE.md`, `adr/README.md`, ADR 0001-0004.
- Existing schema, prompts, skills, docs, and canonical specs.

## Plan Summary

- The current v0.1.2 lifecycle has `review.md` and `test-plan.md`, but those gates are too soft.
- The hardened lifecycle needs an automatic pre-design grill and a post-design grill.
- Matt `grill-with-docs` should update `CONTEXT.md` and ADRs when decisions or terms crystallize.
- Matt `tdd` should become the project-local canonical implementation discipline for behavior-changing apply tasks.

## Question Loop

### Q1: Should the existing `review.md` remain the only grill artifact?

Recommended answer: No. Use two explicit artifacts: `grill.md` before design and `design-review.md` after design. This prevents Codex from designing before domain/context uncertainty is resolved, and then stress-tests the technical design before ADR/test planning.

Resolution: Accepted by the implementation plan.

### Q2: Should Matt TDD replace Superpowers TDD as canonical?

Recommended answer: Yes for this overlay. Vendor Matt `tdd` as the canonical project-local skill so new projects do not depend on an optional plugin. Other TDD skills may supplement it, but `/opsx:apply` should enforce the vendored skill.

Resolution: Accepted by the implementation plan.

### Q3: Where should Matt `CONTEXT.md` fit with the existing constitution/architecture model?

Recommended answer: `CONTEXT.md` is glossary/domain language only. `CONSTITUTION.md` remains policy and rules; `ARCHITECTURE.md` remains the current architecture snapshot; `adr/` remains durable decision history.

Resolution: Accepted by the implementation plan.

## Resolved Terms

- **Grill gate**: mandatory pre-design Matt `grill-with-docs` artifact at `openspec/changes/<change>/grill.md`.
- **Design review gate**: mandatory post-design Matt `grill-with-docs` artifact at `openspec/changes/<change>/design-review.md`.
- **TDD slice**: one vertical RED -> GREEN -> REFACTOR behavior slice through a public interface.

## Document Updates Required

- Update schema and templates for `grill` and `design-review` artifacts.
- Vendor Matt skill files and add OpenSpec adapter notes.
- Update prompts/skills to execute grill/TDD automatically.
- Update canonical specs and docs.
- Add ADR 0005 and update `ARCHITECTURE.md`/`adr/README.md`.

## ADR Candidates

- Durable ADR required: adopting Matt grill/TDD gates is hard to reverse, surprising without context, and involves a real trade-off against the softer v0.1.2 gates.

## Open Questions

None.
