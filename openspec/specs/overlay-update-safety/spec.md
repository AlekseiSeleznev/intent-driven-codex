# overlay-update-safety Specification

## Purpose
Defines update and compatibility boundaries for the project-local overlay, persistent project context, and local secret files.
## Requirements
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
- **THEN** the smoke change creates proposal, specs, grill, design, design-review, ADR, test-plan, and tasks artifacts
- **AND** the apply instructions include `grill`, `design-review`, and `test-plan` context files before the smoke change is cleaned up

### Requirement: Compatibility failures identify the broken layer
The overlay check MUST report whether a failure belongs to OpenSpec CLI changes, local schema validation, Codex command adapters, generated skills, or project artifacts.

#### Scenario: Instructions command changes
- **GIVEN** OpenSpec changes the `openspec instructions` interface
- **WHEN** overlay checks detect the mismatch
- **THEN** Codex reports that the Codex command or skill adapter needs an update

### Requirement: Overlay updates preserve constitution and local secrets
Overlay update and compatibility workflows MUST treat `CONSTITUTION.md` as project-owned rules, `CONTEXT.md`, `ARCHITECTURE.md`, and `adr/` as project-owned architecture context, and `.secrets.local.env` as local-only secret state that must not be overwritten, archived, or exposed.

#### Scenario: Overlay update preserves constitution
- **GIVEN** a repository contains `CONSTITUTION.md`
- **WHEN** Codex installs or updates the intent-driven overlay
- **THEN** Codex does not overwrite the constitution without explicit user approval
- **AND** Codex reports any proposed constitution template changes separately from OpenSpec artifacts

#### Scenario: Overlay update detects legacy constitution
- **GIVEN** a repository contains legacy `PROJECT_CONSTITUTION.md`
- **AND** `CONSTITUTION.md` is missing
- **WHEN** Codex installs or updates the intent-driven overlay
- **THEN** Codex reports a migration opportunity
- **AND** Codex does not silently create a competing `CONSTITUTION.md` with different rules

#### Scenario: Overlay update preserves architecture context
- **GIVEN** a repository contains `CONTEXT.md`, `CONTEXT-MAP.md`, `ARCHITECTURE.md`, `adr/`, or `docs/architecture/`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not overwrite those project-owned files without explicit user approval
- **AND** Codex reports architecture template changes separately from OpenSpec artifacts

#### Scenario: Overlay update ignores local secret values
- **GIVEN** a repository contains `.secrets.local.env`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not print secret values
- **AND** Codex does not stage or commit `.secrets.local.env`

#### Scenario: Overlay health check can run without constitution or architecture
- **GIVEN** `CONSTITUTION.md`, `CONTEXT.md`, or `ARCHITECTURE.md` is missing
- **WHEN** Codex runs overlay health diagnostics
- **THEN** Codex may continue the diagnostic check
- **AND** Codex reports the missing file as project setup guidance rather than an OpenSpec CLI failure
