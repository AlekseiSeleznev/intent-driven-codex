## ADDED Requirements

### Requirement: Schema guidance includes constitution context
The `intent-driven` schema SHALL instruct Codex to account for `PROJECT_CONSTITUTION.md` during artifact creation and apply guidance without making the constitution an OpenSpec artifact.

#### Scenario: Artifact instructions reference constitution context
- **GIVEN** Codex requests instructions for an intent-driven planning artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to consider relevant `PROJECT_CONSTITUTION.md` rules
- **AND** the instruction preserves the artifact's existing OpenSpec output path

#### Scenario: Apply instructions reference constitution context
- **GIVEN** an intent-driven change is apply-ready
- **WHEN** Codex requests `openspec instructions apply --change <change> --json`
- **THEN** the apply instruction tells Codex to account for relevant project constitution rules before implementation
- **AND** Codex still reads the OpenSpec context files returned by the CLI

### Requirement: Constitution guidance does not alter schema lifecycle
The `intent-driven` schema MUST preserve the existing proposal, specs, design, adr, and tasks lifecycle while adding constitution guidance.

#### Scenario: Lifecycle remains unchanged
- **GIVEN** a new change uses the `intent-driven` schema
- **WHEN** Codex checks `openspec status --change <change> --json`
- **THEN** the artifact order remains `proposal`, `specs`, `design`, `adr`, and `tasks`
- **AND** `PROJECT_CONSTITUTION.md` does not appear as an artifact

#### Scenario: Schema remains valid
- **GIVEN** the constitution guidance has been added to the schema
- **WHEN** Codex runs `openspec schema validate intent-driven`
- **THEN** OpenSpec reports the schema as valid
