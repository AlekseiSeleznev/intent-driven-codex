## Why

The README and Russian README currently place the Claude review session controls section after version and license, which makes related Claude review information harder to discover. The controls should live near the optional Claude artifact review explanation where users naturally look for review settings.

## What Changes

- Move the Claude review session controls section in `README.md` from the end of the document to immediately after the optional Claude artifact review section.
- Move the Russian equivalent section in `README.ru.md` to the same logical location after the optional Claude review section.
- Preserve the wording and command coverage, changing only placement unless minor flow adjustments are needed.

## Capabilities

- Modify `codex-opsx-workflow` to require README documentation to group Claude review session controls with Claude review workflow documentation.

## Impact

- Affected files: `README.md`, `README.ru.md`, OpenSpec change artifacts, and canonical specs after archive.
- No code, runtime behavior, external systems, or secrets are changed.
- Verification: README structure checks, `scripts/check-overlay`, OpenSpec validation, diff hygiene, and tracked-secret checks.
