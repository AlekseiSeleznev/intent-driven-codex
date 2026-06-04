## ADDED Requirements

### Requirement: Overlay updates preserve constitution and local secrets
Overlay update and compatibility workflows MUST treat `PROJECT_CONSTITUTION.md` as project-owned rules and `.secrets.local.env` as local-only secret state that must not be overwritten, archived, or exposed.

#### Scenario: Overlay update preserves constitution
- **GIVEN** a repository contains `PROJECT_CONSTITUTION.md`
- **WHEN** Codex installs or updates the intent-driven overlay
- **THEN** Codex does not overwrite the constitution without explicit user approval
- **AND** Codex reports any proposed constitution template changes separately from OpenSpec artifacts

#### Scenario: Overlay update ignores local secret values
- **GIVEN** a repository contains `.secrets.local.env`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not print secret values
- **AND** Codex does not stage or commit `.secrets.local.env`

#### Scenario: Overlay health check can run without constitution
- **GIVEN** `PROJECT_CONSTITUTION.md` is missing
- **WHEN** Codex runs overlay health diagnostics
- **THEN** Codex may continue the diagnostic check
- **AND** Codex reports the missing constitution as project setup guidance rather than an OpenSpec CLI failure
