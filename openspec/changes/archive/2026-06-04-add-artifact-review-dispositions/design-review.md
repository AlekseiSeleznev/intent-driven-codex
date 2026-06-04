# Design Review: add-artifact-review-dispositions

## Review Summary

The design keeps review reports provider-neutral, adds formal dispositions as adjacent local JSON files, and avoids provider calls during validation. It satisfies the user's preference for compact localized chat output while preserving audit detail in files.

## Findings

### Finding 1: Disposition matching needs stable identity

Resolution: Use `source` plus array `index` against the persisted review report. This is deterministic for the current report shape and avoids inventing IDs in provider output.

### Finding 2: Chat language localization should not become a hard-coded Russian-only formatter

Resolution: The helper exposes status facts and display values, while prompts/skills instruct Codex to render in the current chat language. Russian examples are used because the current conversation is Russian.

### Finding 3: Rounding must not destroy audit precision

Resolution: Raw numeric cost fields remain unchanged. Only display fields round upward to kopecks.

## Open Questions

None.
