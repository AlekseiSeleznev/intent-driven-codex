# Test Plan: add-artifact-review-dispositions

## TDD Slices

### Slice 1: RUB display rounding

- RED: Add a local check/fixture proving `0.81953034` renders as `0,82 ₽` and `1.30625318` renders as `1,31 ₽`.
- GREEN: Implement upward rounding display helper in `scripts/openspec-artifact-review`.
- REFACTOR: Keep raw cost summary fields unchanged.

### Slice 2: Disposition summary validation

- RED: Add fixture reports/dispositions where unresolved findings fail and closed findings pass.
- GREEN: Implement `--disposition-summary` validation in `scripts/openspec-artifact-review`.
- REFACTOR: Keep validation provider-free and secret-safe.

### Slice 3: Compact localized status instructions

- RED: Update `scripts/check-overlay` expectations so old `## Artifact Review`, `Budget / cost`, `Short summary`, and `User-facing questions` wording is no longer the required user-facing pattern.
- GREEN: Update prompts, skills, and docs to require the `---` wrapped compact localized block.
- REFACTOR: Deduplicate wording where practical.

## Required Checks

- `openspec validate add-artifact-review-dispositions --strict`
- `openspec validate --all --strict`
- `scripts/check-overlay --no-smoke`
- `scripts/check-overlay`
- `git diff --check`

## Exceptions

None.
