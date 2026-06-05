## Why

Session-level artifact review enablement currently does not affect ordinary `scripts/openspec-artifact-review` runs unless callers manually pass a temporary effective config. This makes `/opsx:review-on` and `scripts/openspec-session-state --review on` appear successful while direct artifact review still skips when raw config has `enabled: false`.

## What Changes

- Make `scripts/openspec-artifact-review` load effective config by default when no explicit `--config` is provided.
- Preserve explicit `--config <path>` priority with no implicit session merge.
- Add a provider-neutral escape hatch (`--raw-config` / `--ignore-session`) for raw config diagnostics.
- Add local regression checks for session `review on`, `review off`, explicit config priority, and raw-config behavior without provider calls.
- Update docs and prompt guidance to explain that session review state affects ordinary artifact-review runs.

## Capabilities

- Modify `artifact-review` capability.

## Impact

- Affected scripts: `scripts/openspec-artifact-review`, `scripts/check-overlay`.
- Affected docs/prompts/skills: artifact review/session state documentation and review workflow guidance.
- No new external systems or secrets. Provider API keys remain local-only and only env-var presence may be reported.
