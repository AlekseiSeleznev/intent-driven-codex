## MODIFIED Requirements

### Requirement: Overlay compatibility can be checked after OpenSpec updates
The template SHALL provide `/opsx:check-overlay` or an equivalent command that validates the installed overlay and expanded intent-driven lifecycle against the current OpenSpec CLI.

#### Scenario: Overlay check validates schema
- **GIVEN** the `intent-driven` schema is installed
- **WHEN** Codex runs overlay checks
- **THEN** Codex runs `openspec schema validate intent-driven`
- **AND** reports schema validation failures with the affected layer

#### Scenario: Overlay check uses valid OpenSpec status commands
- **GIVEN** OpenSpec 1.3.1 requires `--change` for `openspec status --json`
- **WHEN** Codex runs overlay checks
- **THEN** Codex uses `openspec list --json` or a temporary smoke change with `openspec status --change <name> --json`
- **AND** Codex does not run bare `openspec status --json`

#### Scenario: Overlay check validates expanded lifecycle smoke
- **GIVEN** the expanded `intent-driven` schema is installed
- **WHEN** Codex runs the full overlay smoke check
- **THEN** the smoke change creates proposal, specs, design, review, ADR, test-plan, and tasks artifacts
- **AND** the apply instructions include `review` and `test-plan` context files before the smoke change is cleaned up
