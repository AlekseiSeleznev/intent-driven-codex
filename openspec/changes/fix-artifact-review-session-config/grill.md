# Grill: fix-artifact-review-session-config

## Scope Under Review

Fix session-level artifact review enablement so ordinary `scripts/openspec-artifact-review` runs respect `.codex/session/openspec-session.json` unless an explicit config or raw-config escape hatch is used.

## Evidence Reviewed

- `scripts/openspec-session-state --effective-review-config` already merges base config and session state.
- `scripts/openspec-artifact-review` currently loads only `args.config` or `.codex/openspec-artifact-review.json`.
- Existing prompts often work around this by generating a temp effective config, but direct helper UX remains broken.

## Material Questions

### Q1: Should explicit `--config` still bypass session state?

Decision: Yes. Explicit config is a deterministic test/diagnostic input and must not be changed implicitly.

### Q2: Should the fix hard-code Polza/DeepSeek behavior?

Decision: No. Merge only config/session fields; do not inspect model/provider names beyond existing validation.

### Q3: What raw diagnostics escape hatch should be supported?

Decision: Support both `--raw-config` and alias `--ignore-session` for clear CLI UX.

## Open Questions

None.
