# template-installation Specification

## Purpose
Defines safe Greenfield/Brownfield installation, installed documentation, generated goal guidance, and project context setup for the reusable template.
## Requirements
### Requirement: Greenfield installation is repeatable
The template SHALL support installation into a new repository and leave it ready to create intent-driven OpenSpec changes with mandatory review and test-plan gates immediately.

#### Scenario: Greenfield install enables intent-driven schema
- **GIVEN** a new repository has OpenSpec initialized for Codex
- **WHEN** the template assets are installed
- **THEN** `openspec/config.yaml` selects `schema: intent-driven`
- **AND** `openspec schema validate intent-driven` succeeds

#### Scenario: Greenfield smoke check reaches apply-ready state
- **GIVEN** the template is installed in a new repository
- **WHEN** Codex runs the full smoke check
- **THEN** a smoke change can create proposal, specs, design, review, ADR review, test-plan, and tasks artifacts
- **AND** `openspec instructions apply --change <smoke> --json` returns ready context including review and test-plan files

### Requirement: Brownfield installation is conservative
The template SHALL support existing repositories without overwriting existing OpenSpec changes, canonical specs, ADRs, project context, or project documentation.

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
The template MUST include documentation that explains what is invoked at each lifecycle stage, mandatory `grill-with-docs` review, mandatory test-first verification planning, generated Codex Goal prompts for long apply and bulk-apply runs, Greenfield flow, Brownfield flow, git checkpoints, ADR lifecycle, and update safety.

#### Scenario: User reads installed functionality docs
- **GIVEN** the template has been installed
- **WHEN** the user opens the workflow documentation
- **THEN** the documentation describes each `/opsx:*` command and its OpenSpec/skill behavior
- **AND** it identifies which behaviors are mandatory and which are optional
- **AND** it explains that development and enhancement changes pass through `review.md` and `test-plan.md` before implementation
- **AND** it explains that long `/opsx:apply` and eligible `/opsx:bulk-apply` runs can generate a copy-paste `/goal` prompt before implementation begins

#### Scenario: User learns how to run a generated apply goal
- **GIVEN** a project has the template documentation installed
- **WHEN** the user reads the apply or bulk-apply guidance
- **THEN** the documentation shows that the generated prompt starts with `/goal`
- **AND** the prompt body includes the concrete `/opsx:apply <change>` or `/opsx:bulk-apply <changes...>` workflow, completion criteria, and stop-without-completion conditions
- **AND** the documentation states that the user should copy and send the generated prompt as the next message when they want Codex Goal to manage the run

#### Scenario: User learns when goal guidance is skipped
- **GIVEN** a project has the template documentation installed
- **WHEN** the user reads the apply or bulk-apply guidance
- **THEN** the documentation explains that goal guidance is skipped when the run is already inside an active goal, the work is small enough for normal apply, or the user explicitly requests to continue without a goal
- **AND** the documentation states that OpenSpec artifacts remain the source of truth even when Codex Goal manages execution

### Requirement: Installation provides constitution and secret templates safely
The template SHALL provide `CONSTITUTION.md`, `ARCHITECTURE.md`, and local-secret setup guidance that is safe for Greenfield and Brownfield repositories.

#### Scenario: Greenfield install explains constitution setup
- **GIVEN** the template is installed into a new repository
- **WHEN** the user reads the installed documentation
- **THEN** the documentation explains that `CONSTITUTION.md` is a Git-tracked project rules file
- **AND** it explains the required constitution sections
- **AND** it explains that secret values belong only in local `.secrets.local.env`

#### Scenario: Greenfield install explains architecture setup
- **GIVEN** the template is installed into a new repository
- **WHEN** the user reads the installed documentation
- **THEN** the documentation explains that `ARCHITECTURE.md` is the current architecture snapshot
- **AND** it explains that durable decisions remain in `adr/`

#### Scenario: Local secrets are ignored by Git
- **GIVEN** the template is installed in a repository
- **WHEN** Codex checks the installation files
- **THEN** `.secrets.local.env` is covered by ignore guidance or `.gitignore`
- **AND** tracked examples document only variable names or empty placeholders

#### Scenario: Brownfield install preserves existing context
- **GIVEN** a repository already contains `CONSTITUTION.md`, legacy `PROJECT_CONSTITUTION.md`, `ARCHITECTURE.md`, or `adr/`
- **WHEN** the template installer or update guidance runs
- **THEN** it does not overwrite the existing context silently
- **AND** it asks before replacing or materially editing project-owned rules or architecture

### Requirement: Installed documentation explains constitution workflow behavior
The template documentation MUST explain when Codex reads the project constitution, how missing constitution bootstrap works, how external credentials are resolved, how architecture context is loaded, and how conflicts are handled.

#### Scenario: User learns when constitution is read
- **GIVEN** the template documentation is installed
- **WHEN** the user reads the workflow documentation
- **THEN** it explains that `/opsx:*` workflows run a constitution preflight
- **AND** it explains that OpenSpec CLI itself does not read the constitution

#### Scenario: User learns how architecture is read
- **GIVEN** the template documentation is installed
- **WHEN** the user reads the workflow documentation
- **THEN** it explains that architecture-sensitive workflows read `ARCHITECTURE.md` and relevant in-force ADRs
- **AND** it explains that those files are persistent project context outside the OpenSpec change artifact graph

#### Scenario: User learns how secrets are referenced
- **GIVEN** the template documentation is installed
- **WHEN** the user reads secret-handling guidance
- **THEN** it shows that `CONSTITUTION.md` contains credential variable names only
- **AND** it shows that `.secrets.local.env` contains local values for those variables
- **AND** it warns not to commit real secret values
