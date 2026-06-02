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

### Requirement: Installation documentation remains executable
The template MUST keep Greenfield, Brownfield, and manual installation instructions aligned with the installer behavior and the files required by `scripts/check-overlay`, so a user following the documented path can reach a green overlay check without undocumented file copies.

#### Scenario: Greenfield docs use force overlay replacement
- **GIVEN** a Greenfield repository has just run `openspec init . --tools codex`
- **WHEN** the user follows the template's Greenfield installation instructions
- **THEN** the documented installer command includes `--force-overlay`
- **AND** placeholder OpenSpec/Codex prompts and skills are replaced by the intent-driven overlay assets

#### Scenario: Manual install lists all required overlay assets
- **GIVEN** a user follows the manual installation procedure instead of `scripts/install-overlay`
- **WHEN** the user copies the documented files into the target repository
- **THEN** the documented list includes every script, config, prompt, skill, doc, and ignore rule required for `scripts/check-overlay` to pass
- **AND** the manual path does not omit `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, `scripts/openspec-session-state`, `scripts/openspec-git-checkpoint`, or `.codex/session/` ignore guidance

#### Scenario: Greenfield docs do not overwrite context after install
- **GIVEN** the installer has already created or preserved `CONSTITUTION.md`, `ARCHITECTURE.md`, and local secret examples
- **WHEN** the user reads the post-install documentation steps
- **THEN** the instructions tell the user to review and edit those files rather than blindly copy over them
- **AND** existing project-owned context is not overwritten by a bare `cp` command

### Requirement: Release documentation identifies the shipped template state
The template SHALL keep version, changelog, README badges, current-state narrative, active change list, archived change list, and in-force ADR summaries consistent with the repository state being shipped.

#### Scenario: Version and changelog describe recent features
- **GIVEN** a release includes new Claude review, session-control, git-checkpoint, or documentation-fix behavior
- **WHEN** a user reads `VERSION`, `CHANGELOG.md`, and README state sections
- **THEN** the version and changelog describe the shipped feature set consistently
- **AND** README badges and state text do not advertise an older feature baseline

#### Scenario: README state matches OpenSpec state
- **GIVEN** there are no active OpenSpec changes
- **WHEN** a user reads the README current-state table
- **THEN** the table says there are no active changes
- **AND** archived changes listed or referenced by the README match `openspec/changes/archive/`

### Requirement: Auto-repair installer preserves the selected share source
The `opsx install-auto-repair` command SHALL never delete the source tree it is about to install from. When the resolved source root and share root are the same directory, installation SHALL preserve that directory and still install executable shims safely.

#### Scenario: Source root equals share root
- **GIVEN** `opsx install-auto-repair --source-root <root> --share-root <root>` is invoked
- **AND** `<root>` contains the repair manifest and shim source files
- **WHEN** installation runs
- **THEN** `<root>` is not removed or replaced before copying executables
- **AND** the command installs or refreshes `opsx` and the OpenSpec shim in the requested bin directory
- **AND** the persisted state records the preserved share root

#### Scenario: Different source and share roots still refresh share copy
- **GIVEN** `opsx install-auto-repair --source-root <source> --share-root <share>` is invoked with different resolved directories
- **WHEN** installation runs
- **THEN** the share root is refreshed from the source root using the documented ignore rules
- **AND** the command installs or refreshes `opsx` and the OpenSpec shim in the requested bin directory

### Requirement: Install checks detect stale real OpenSpec paths
The auto-repair install/update tooling SHALL include a post-install sanity check that compares the shim-resolved real OpenSpec binary with the current real OpenSpec CLI and verifies both report the same current version. If the state file points at a stale or missing binary, the check SHALL fail clearly before reporting installation success.

#### Scenario: Real OpenSpec path is current
- **GIVEN** auto-repair installation records a real OpenSpec binary path
- **WHEN** the post-install sanity check runs
- **THEN** invoking the installed shim for `--version` reports the same version as invoking the recorded real OpenSpec binary directly
- **AND** the command reports the checked real OpenSpec path

#### Scenario: Real OpenSpec path is stale
- **GIVEN** the auto-repair state points at an old, missing, or different real OpenSpec binary
- **WHEN** the post-install sanity check runs
- **THEN** the check fails with a clear stale-real-OpenSpec diagnostic
- **AND** the user is told which non-secret path must be refreshed
- **AND** the command does not claim the install/update check passed

