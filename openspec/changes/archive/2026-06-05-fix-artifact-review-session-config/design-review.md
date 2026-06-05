# Design Review: fix-artifact-review-session-config

## Review Summary

The design fixes the UX at the helper boundary rather than requiring every caller to remember `--effective-review-config`. Explicit `--config` remains deterministic, and raw config diagnostics are preserved through `--raw-config` / `--ignore-session`.

## Findings

### Finding 1: Duplicate merge logic can drift from `openspec-session-state`

Resolution: Keep merge semantics intentionally small and identical for artifact review fields: decision, provider, and stage overlays. Cover the behavior with `scripts/check-overlay` fixtures.

### Finding 2: Session state might contain malformed or secret-looking keys

Resolution: Reuse the existing allowed schema concepts and ignore unsupported keys in artifact-review merge. Validation still runs on the resulting config and reports only sanitized provider metadata.

### Finding 3: Existing prompts already pass temp effective configs

Resolution: Update docs/prompts to state ordinary helper runs now use session-effective config by default, while keeping explicit config use valid for deterministic checks.

## Open Questions

None.
