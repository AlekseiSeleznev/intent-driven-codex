## Context Read

- `openspec/changes/move-readme-claude-review-controls/proposal.md`
- `openspec/changes/move-readme-claude-review-controls/specs/codex-opsx-workflow/spec.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `README.md`
- `README.ru.md`
- `openspec/specs/codex-opsx-workflow/spec.md`
- `docs/lifecycle.md`
- `docs/update-safety.md`

## Plan Summary

- The Claude review session controls section is currently at the very end of both READMEs after Version and License.
- The optional Claude artifact review section already explains configuration, model/effort, budget, and session budget exhaustion.
- Moving session controls immediately after optional Claude artifact review keeps related content together without changing command behavior.
- The Russian README should mirror the English structure and keep natural Russian wording.

## Question Loop

### Q1: Should the section move into the command table instead of after optional Claude review?

**Recommended answer:** No. Keep the command table as a compact reference and move the explanatory controls block after optional Claude review.

**Rationale:** The command table already lists the `/opsx:claude-review-*` commands. The explanatory block is workflow context and fits best next to review configuration and budget behavior.

**Resolution:** Move the block after optional Claude artifact review in both READMEs.

## Resolved Terms

None. This is documentation structure, not domain terminology.

## Document Updates Applied

None yet; implementation will move existing sections without changing behavior.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None.

## Open Questions

None.
