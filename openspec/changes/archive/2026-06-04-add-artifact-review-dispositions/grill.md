# Grill: add-artifact-review-dispositions

## Scope Under Review

Add formal artifact review finding dispositions and compact localized status output.

## Evidence Reviewed

- User requested compact status output with full localization to the chat language.
- User approved a Russian example wrapped in `---` separators.
- Existing `artifact-review` spec already requires finding disposition before progress, but only as prose in prompts/skills.
- Existing cost ledger already records raw RUB costs for full-change totals.

## Material Questions

### Q1: Should user-facing labels expose machine names like `mustFix` / `shouldFix`?

Decision: No. User-facing output must be fully localized. Machine keys remain stable in JSON/report/disposition files.

### Q2: Should detailed per-finding text appear in the chat status?

Decision: No by default. Chat output should stay compact and show only budget/cost plus aggregate status. Detailed findings appear only when user participation is required or in persisted reports/dispositions.

### Q3: How should money be displayed?

Decision: Displayed RUB costs round upward to kopecks. Raw costs remain in machine-readable JSON for audit.

## Open Questions

None.
