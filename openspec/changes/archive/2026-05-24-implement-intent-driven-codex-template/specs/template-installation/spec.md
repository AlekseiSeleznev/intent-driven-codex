## ADDED Requirements

### Requirement: Greenfield installation is repeatable
The template SHALL support installation into a new repository and leave it ready to create intent-driven OpenSpec changes immediately.

#### Scenario: Greenfield install enables intent-driven schema
- **GIVEN** a new repository has OpenSpec initialized for Codex
- **WHEN** the template assets are installed
- **THEN** `openspec/config.yaml` selects `schema: intent-driven`
- **AND** `openspec schema validate intent-driven` succeeds

#### Scenario: Greenfield smoke check reaches apply-ready state
- **GIVEN** the template is installed in a new repository
- **WHEN** Codex runs the full smoke check
- **THEN** a smoke change can create proposal, specs, design, ADR review, and tasks artifacts
- **AND** `openspec instructions apply --change <smoke> --json` returns ready context

### Requirement: Brownfield installation is conservative
The template SHALL support existing repositories without overwriting existing OpenSpec changes, canonical specs, ADRs, or project documentation.

#### Scenario: Brownfield install detects existing OpenSpec assets
- **GIVEN** a repository already contains `openspec/` files
- **WHEN** the template installer runs
- **THEN** it reports existing schemas, specs, changes, and config
- **AND** it asks before replacing any template-owned file

#### Scenario: Brownfield change modifies existing capabilities safely
- **GIVEN** canonical specs already exist under `openspec/specs/`
- **WHEN** Codex creates a new intent-driven change for existing behavior
- **THEN** the proposal lists modified capabilities only for behavior-level changes
- **AND** delta specs use `MODIFIED Requirements` with complete updated requirement blocks

### Requirement: Installed documentation explains workflow behavior
The template MUST include documentation that explains what is invoked at each lifecycle stage, optional skill conditions, Greenfield flow, Brownfield flow, git checkpoints, ADR lifecycle, and update safety.

#### Scenario: User reads installed functionality docs
- **GIVEN** the template has been installed
- **WHEN** the user opens the workflow documentation
- **THEN** the documentation describes each `/opsx:*` command and its OpenSpec/skill behavior
- **AND** it identifies which behaviors are mandatory and which are optional
