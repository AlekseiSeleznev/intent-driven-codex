## ADDED Requirements

### Requirement: Installation provides constitution and secret templates safely
The template SHALL provide project constitution and local-secret setup guidance that is safe for Greenfield and Brownfield repositories.

#### Scenario: Greenfield install explains constitution setup
- **GIVEN** the template is installed into a new repository
- **WHEN** the user reads the installed documentation
- **THEN** the documentation explains that `PROJECT_CONSTITUTION.md` is a Git-tracked project rules file
- **AND** it explains the required constitution sections
- **AND** it explains that secret values belong only in local `.secrets.local.env`

#### Scenario: Local secrets are ignored by Git
- **GIVEN** the template is installed in a repository
- **WHEN** Codex checks the installation files
- **THEN** `.secrets.local.env` is covered by ignore guidance or `.gitignore`
- **AND** tracked examples document only variable names or empty placeholders

#### Scenario: Brownfield install preserves existing constitution
- **GIVEN** a repository already contains `PROJECT_CONSTITUTION.md`
- **WHEN** the template installer or update guidance runs
- **THEN** it does not overwrite the existing constitution silently
- **AND** it asks before replacing or materially editing project-owned rules

### Requirement: Installed documentation explains constitution workflow behavior
The template documentation MUST explain when Codex reads the project constitution, how missing constitution bootstrap works, how external credentials are resolved, and how conflicts are handled.

#### Scenario: User learns when constitution is read
- **GIVEN** the template documentation is installed
- **WHEN** the user reads the workflow documentation
- **THEN** it explains that `/opsx:*` workflows run a constitution preflight
- **AND** it explains that OpenSpec CLI itself does not read the constitution

#### Scenario: User learns how secrets are referenced
- **GIVEN** the template documentation is installed
- **WHEN** the user reads secret-handling guidance
- **THEN** it shows that `PROJECT_CONSTITUTION.md` contains credential variable names only
- **AND** it shows that `.secrets.local.env` contains local values for those variables
- **AND** it warns not to commit real secret values
