## MODIFIED Requirements

### Requirement: Overlay updates preserve constitution and local secrets
Overlay update and compatibility workflows MUST treat `CONSTITUTION.md` as project-owned rules, `ARCHITECTURE.md` and `adr/` as project-owned architecture context, and `.secrets.local.env` as local-only secret state that must not be overwritten, archived, or exposed.

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
- **GIVEN** a repository contains `ARCHITECTURE.md`, `adr/`, or `docs/architecture/`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not overwrite those project-owned files without explicit user approval
- **AND** Codex reports architecture template changes separately from OpenSpec artifacts

#### Scenario: Overlay update ignores local secret values
- **GIVEN** a repository contains `.secrets.local.env`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not print secret values
- **AND** Codex does not stage or commit `.secrets.local.env`

#### Scenario: Overlay health check can run without constitution or architecture
- **GIVEN** `CONSTITUTION.md` or `ARCHITECTURE.md` is missing
- **WHEN** Codex runs overlay health diagnostics
- **THEN** Codex may continue the diagnostic check
- **AND** Codex reports the missing file as project setup guidance rather than an OpenSpec CLI failure
