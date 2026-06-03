## ADDED Requirements

### Requirement: The template is an overlay on OpenSpec
The implementation MUST use OpenSpec public CLI behavior and project-local assets rather than modifying or forking OpenSpec.

#### Scenario: OpenSpec remains externally owned
- **GIVEN** the template is installed in a repository
- **WHEN** OpenSpec is upgraded by the user's package manager
- **THEN** the template files remain project-local
- **AND** OpenSpec core files are not patched by the template

#### Scenario: Existing artifacts are not overwritten by update checks
- **GIVEN** a repository contains active changes, canonical specs, or durable ADRs
- **WHEN** Codex runs overlay compatibility checks
- **THEN** existing project artifacts are read and validated
- **AND** they are not overwritten without explicit user approval

### Requirement: Overlay compatibility can be checked after OpenSpec updates
The template SHALL provide `/opsx:check-overlay` or an equivalent command that validates the installed overlay against the current OpenSpec CLI.

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

### Requirement: Compatibility failures identify the broken layer
The overlay check MUST report whether a failure belongs to OpenSpec CLI changes, local schema validation, Codex command adapters, generated skills, or project artifacts.

#### Scenario: Instructions command changes
- **GIVEN** OpenSpec changes the `openspec instructions` interface
- **WHEN** overlay checks detect the mismatch
- **THEN** Codex reports that the Codex command or skill adapter needs an update
