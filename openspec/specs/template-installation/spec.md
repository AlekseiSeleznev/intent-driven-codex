# template-installation Specification

## Purpose
Defines safe Greenfield/Brownfield installation, installed documentation, generated goal guidance, and project context setup for the reusable template.

## Requirements

### Requirement: Greenfield installation is repeatable
The template SHALL support installation into a new repository and leave it ready to create intent-driven OpenSpec changes with mandatory grill, design-review, and TDD gates immediately.

#### Scenario: Greenfield install enables intent-driven schema
- **GIVEN** a new repository has OpenSpec initialized for Codex
- **WHEN** the template assets are installed
- **THEN** `openspec/config.yaml` selects `schema: intent-driven`
- **AND** `openspec schema validate intent-driven` succeeds

#### Scenario: Greenfield smoke check reaches apply-ready state
- **GIVEN** the template is installed in a new repository
- **WHEN** Codex runs the full smoke check
- **THEN** a smoke change can create proposal, specs, grill, design, design-review, ADR review, test-plan, and tasks artifacts
- **AND** `openspec instructions apply --change <smoke> --json` returns ready context including grill, design-review, and test-plan files

### Requirement: Brownfield installation is conservative
The template SHALL support existing repositories without overwriting existing OpenSpec changes, canonical specs, ADRs, project context, or project documentation.

#### Scenario: Brownfield install detects existing OpenSpec assets
- **GIVEN** a repository already contains `openspec/` files
- **WHEN** the template installer runs
- **THEN** it reports existing schemas, specs, changes, and config
- **AND** it asks before replacing any template-owned file

#### Scenario: Brownfield install preserves existing context
- **GIVEN** a repository already contains `CONSTITUTION.md`, `CONTEXT.md`, `CONTEXT-MAP.md`, `ARCHITECTURE.md`, or `adr/`
- **WHEN** the template installer or update guidance runs
- **THEN** it does not overwrite the existing context silently
- **AND** it asks before replacing or materially editing project-owned rules, glossary, or architecture

### Requirement: Installed documentation explains workflow behavior
The template MUST include documentation that explains each lifecycle stage, mandatory Matt grill gates, mandatory Matt TDD slices, generated Codex Goal prompts for long apply and bulk-apply runs, Greenfield flow, Brownfield flow, git checkpoints, ADR lifecycle, and update safety.

#### Scenario: User reads installed functionality docs
- **GIVEN** the template has been installed
- **WHEN** the user opens the workflow documentation
- **THEN** the documentation describes each `/opsx:*` command and its OpenSpec/skill behavior
- **AND** it explains that development and enhancement changes pass through `grill.md`, `design-review.md`, and `test-plan.md` before implementation
- **AND** it explains that long `/opsx:apply` and eligible `/opsx:bulk-apply` runs can generate a copy-paste `/goal` prompt before implementation begins

### Requirement: Installation provides context and secret templates safely
The template SHALL provide `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, and local-secret setup guidance that is safe for Greenfield and Brownfield repositories.

#### Scenario: Greenfield install explains context setup
- **GIVEN** the template is installed into a new repository
- **WHEN** the user reads the installed documentation
- **THEN** the documentation explains that `CONSTITUTION.md` is a Git-tracked project rules file
- **AND** it explains that `CONTEXT.md` is glossary-only
- **AND** it explains that `ARCHITECTURE.md` is the current architecture snapshot
- **AND** it explains that secret values belong only in local `.secrets.local.env`

#### Scenario: Local secrets are ignored by Git
- **GIVEN** the template is installed in a repository
- **WHEN** Codex checks the installation files
- **THEN** `.secrets.local.env` is covered by ignore guidance or `.gitignore`
- **AND** tracked examples document only variable names or empty placeholders

### Requirement: Installed overlays include Matt grill and TDD assets
The installer and global overlay update process MUST install the vendored `grill-with-docs` and `tdd` skills, related reference files, schema templates, prompts, and docs needed for the hardened lifecycle.

#### Scenario: Greenfield install includes hardened skills
- **GIVEN** a target project receives the overlay with `scripts/install-overlay --force-overlay`
- **WHEN** installation completes
- **THEN** `.codex/skills/grill-with-docs` contains Matt grill guidance and context/ADR format references
- **AND** `.codex/skills/tdd` contains Matt TDD guidance and supporting references

#### Scenario: Overlay check validates hardened lifecycle
- **GIVEN** the overlay checker runs in a project with the intent-driven schema
- **WHEN** it creates a smoke change
- **THEN** it expects the artifact order `proposal`, `specs`, `grill`, `design`, `design-review`, `adr`, `test-plan`, `tasks`
- **AND** it verifies apply context includes the hardened gate files
