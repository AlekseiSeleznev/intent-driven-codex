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

### Requirement: Documentation drift checks cover repository state claims
Overlay verification SHALL include automated or documented checks that compare public documentation claims against repository state for active changes, archived changes, in-force ADRs, installer-owned assets, and common command paths.

#### Scenario: Active and archived change claims are checkable
- **GIVEN** README documentation states active and archived OpenSpec change state
- **WHEN** Codex runs documentation drift checks
- **THEN** the checks compare active changes with `openspec list --json`
- **AND** archived change claims are compared with `openspec/changes/archive/`

#### Scenario: Installer manual list is checkable
- **GIVEN** installation documentation lists manual copy assets
- **WHEN** Codex runs documentation drift checks
- **THEN** the checks can compare that list with the files copied by `scripts/install-overlay` and required by `scripts/check-overlay`
- **AND** omissions are reported before release documentation is considered current

#### Scenario: Overlay check command path is spelled correctly
- **GIVEN** documentation tells users how to run the overlay checker
- **WHEN** Codex scans the documentation
- **THEN** references use `scripts/check-overlay`
- **AND** the obsolete spelling `script/check-overlay` is not present

### Requirement: Overlay checks guard Claude review block guidance
Overlay verification MUST include deterministic checks that relevant project-local prompts, skills, documentation, and helper report paths preserve the mandatory user-facing Claude review block behavior.

#### Scenario: Prompt and skill checks require review block markers
- **GIVEN** a repository contains the intent-driven Codex overlay
- **WHEN** `scripts/check-overlay` runs
- **THEN** it checks every prompt or skill that invokes `scripts/openspec-claude-review` for `## Claude Review` guidance
- **AND** it checks for `Budget / cost`, `maxBudgetUsd`, the no-user-question phrase, and the no-unhandled-finding checkpoint rule

#### Scenario: Documentation checks cover review block expectations
- **GIVEN** lifecycle or update-safety documentation describes Claude artifact review
- **WHEN** `scripts/check-overlay` runs documentation drift checks
- **THEN** it verifies the documentation states the required `## Claude Review` block and budget/question behavior

#### Scenario: Helper reports expose configured budget cap
- **GIVEN** `scripts/openspec-claude-review` produces a structured report for a skipped, parsed, unavailable, or real review result
- **WHEN** `scripts/check-overlay` validates reviewer report shape
- **THEN** the report includes `budgetLimitUsd`
- **AND** Codex can use that field to display the configured `maxBudgetUsd` or `none` in the user-facing block
