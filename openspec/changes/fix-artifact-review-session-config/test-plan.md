# Test Plan: fix-artifact-review-session-config

## TDD Slices

### Slice 1: Session `review on` affects ordinary dry-run

- RED: Add/check a fixture where raw config is disabled, session `--review on` is set, and ordinary `scripts/openspec-artifact-review --dry-run` must not return `skipped`.
- GREEN: Implement default session-effective config loading.
- REFACTOR: Keep config source reporting secret-safe.

### Slice 2: Session `review off` and explicit config priority

- RED: Add fixtures proving `--review off` skips ordinary dry-run and explicit disabled `--config` ignores enabled session state.
- GREEN: Preserve explicit config priority and implement raw-config escape hatch.
- REFACTOR: Ensure no provider-specific assumptions.

### Slice 3: Docs and overlay validation

- RED: `scripts/check-overlay` should fail if docs/prompts do not mention ordinary helper session behavior and raw-config escape hatch.
- GREEN: Update docs/prompts/skills as needed.
- REFACTOR: Keep wording concise and provider-neutral.

## Required Checks

- `openspec validate fix-artifact-review-session-config --strict`
- `openspec validate --all --strict`
- `openspec schema validate intent-driven`
- `git diff --check`
- `scripts/check-overlay --no-smoke`
- `scripts/check-overlay`
- tracked-secret guard for local secret files and key-looking values

## Exceptions

None.
