## MODIFIED Requirements

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
- **GIVEN** a repository already contains `CONSTITUTION.md`, `PROJECT_CONSTITUTION.md`, `ARCHITECTURE.md`, or `adr/`
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
