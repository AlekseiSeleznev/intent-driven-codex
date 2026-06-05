# Design: fix-artifact-review-session-config

## Current Problem

`openspec-session-state` owns session overlay state and can emit an effective config, but `openspec-artifact-review` does not consume that state by default. As a result, a user can enable review in session state and still get `verdict=skipped` from direct artifact-review calls when tracked config has `enabled: false`.

## Design

Add session-aware config loading to `scripts/openspec-artifact-review`:

1. If `--config <path>` is provided, load that path exactly and mark source as `explicit`.
2. Else if `--raw-config` or `--ignore-session` is provided, load tracked `.codex/openspec-artifact-review.json` exactly and mark source as `raw`.
3. Else load tracked config and merge `.codex/session/openspec-session.json` using the same provider-neutral rules as `scripts/openspec-session-state --effective-review-config`:
   - `artifactReview.decision=enabled` sets top-level `enabled=true`.
   - `artifactReview.decision=disabled` sets top-level `enabled=false`.
   - `artifactReview.decision=unset` leaves tracked top-level `enabled` unchanged.
   - session `artifactReview.provider` deep-merges provider fields.
   - session `artifactReview.stages.<stage>` deep-merges stage fields.

Use `CODEX_OPENSPEC_SESSION_FILE` when set; otherwise default to `.codex/session/openspec-session.json`. Missing session file is equivalent to unset.

## Reporting

Add non-secret report metadata such as:

```json
"configSource": {
  "mode": "session-effective",
  "configPath": ".codex/openspec-artifact-review.json",
  "sessionPath": ".codex/session/openspec-session.json",
  "sessionDecision": "enabled"
}
```

Do not report secret values. Existing provider sanitization only reports `apiKeyEnv` and `apiKeyPresent`.

## Regression Strategy

Extend `scripts/check-overlay` using temp files and `CODEX_OPENSPEC_SESSION_FILE`:

- raw disabled + `--review on` + ordinary `--dry-run` => not skipped;
- `--review off` + ordinary `--dry-run` => skipped;
- enabled session + explicit disabled `--config` => skipped;
- enabled session + `--raw-config`/`--ignore-session` => skipped;
- no real provider call and no API key required.
