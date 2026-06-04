## Why

Artifact reviewer output is currently verbose, partly English, and relies on prose to prove that reviewer findings were independently handled. Users need compact localized status output plus a machine-readable disposition trail so lifecycle gates cannot silently proceed with unresolved review findings.

## What Changes

- Add formal artifact review finding disposition records for `mustFix`, actionable `shouldFix`, warnings, and questions.
- Require compact localized user-facing review status bounded by `---` separators.
- Round displayed monetary costs upward to minor currency units, especially RUB kopecks.
- Update prompts, skills, docs, and overlay checks so dependent lifecycle progress requires all review findings to be dispositioned.

## Capabilities

- Modify `artifact-review` capability.

## Impact

- Affected code: `scripts/openspec-artifact-review`, `scripts/check-overlay`.
- Affected Codex overlay: `.codex/prompts/opsx-*`, `.codex/skills/openspec-*`.
- Affected docs/specs: `docs/lifecycle.md`, `docs/update-safety.md`, `openspec/specs/artifact-review/spec.md` after archive.
- No new external systems or secrets. Existing `POLZA_AI_API_KEY` remains local-only and must not be recorded in reports or disposition files.
