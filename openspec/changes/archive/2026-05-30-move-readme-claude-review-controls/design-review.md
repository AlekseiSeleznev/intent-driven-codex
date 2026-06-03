## Context Read

- `openspec/changes/move-readme-claude-review-controls/proposal.md`
- `openspec/changes/move-readme-claude-review-controls/specs/codex-opsx-workflow/spec.md`
- `openspec/changes/move-readme-claude-review-controls/grill.md`
- `openspec/changes/move-readme-claude-review-controls/design.md`
- `CONSTITUTION.md`
- `README.md`
- `README.ru.md`

## Design Summary

- Move, do not rewrite, the Claude review session controls blocks.
- Place them after the optional Claude artifact review sections in both languages.
- Keep Version and License as document-ending metadata.
- Verify with ordering checks and standard overlay validation.

## Question Loop

No material questions. Repository context and user request answer placement and scope.

## Design Findings

- **Finding:** The command table already includes all session-control commands.  
  **Resolution:** Preserve command table entries; move only the explanatory block.
- **Finding:** Russian docs should not introduce awkward calques during the move.  
  **Resolution:** Preserve the existing natural Russian paragraph and only relocate it.

## Document Updates Applied

None.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None.

## Open Questions

None.
