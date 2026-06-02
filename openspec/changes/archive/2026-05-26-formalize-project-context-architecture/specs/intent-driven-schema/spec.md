## MODIFIED Requirements

### Requirement: Schema guidance includes persistent project context
The `intent-driven` schema SHALL instruct Codex to account for root `CONSTITUTION.md`, root `ARCHITECTURE.md`, and in-force ADR context during artifact creation and apply guidance without making those files OpenSpec change artifacts.

#### Scenario: Artifact instructions reference project context
- **GIVEN** Codex requests instructions for an intent-driven planning artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to consider relevant `CONSTITUTION.md`, `ARCHITECTURE.md`, and ADR rules or decisions
- **AND** the instruction preserves the artifact's existing OpenSpec output path

#### Scenario: Apply instructions reference project context
- **GIVEN** an intent-driven change is apply-ready
- **WHEN** Codex requests `openspec instructions apply --change <change> --json`
- **THEN** the apply instruction tells Codex to account for relevant constitution, architecture, and ADR context before implementation
- **AND** Codex still reads the OpenSpec context files returned by the CLI

### Requirement: Persistent context guidance does not alter schema lifecycle
The `intent-driven` schema MUST preserve the existing proposal, specs, design, adr, and tasks lifecycle while adding persistent project context guidance.

#### Scenario: Lifecycle remains unchanged
- **GIVEN** a new change uses the `intent-driven` schema
- **WHEN** Codex checks `openspec status --change <change> --json`
- **THEN** the artifact order remains `proposal`, `specs`, `design`, `adr`, and `tasks`
- **AND** `CONSTITUTION.md`, `ARCHITECTURE.md`, and `adr/*.md` do not appear as change artifacts

#### Scenario: Schema remains valid
- **GIVEN** the persistent project context guidance has been added to the schema
- **WHEN** Codex runs `openspec schema validate intent-driven`
- **THEN** OpenSpec reports the schema as valid
