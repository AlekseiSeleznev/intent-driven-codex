## MODIFIED Requirements

### Requirement: Artifact lifecycle follows intent-driven order
The `intent-driven` schema MUST guide development and enhancement changes through `proposal`, `specs`, `design`, `review`, `adr`, `test-plan`, and `tasks` in that order before implementation is allowed.

#### Scenario: Proposal unlocks specs before design
- **GIVEN** a new change uses the `intent-driven` schema
- **WHEN** `proposal.md` exists and no spec files exist
- **THEN** the `specs` artifact is ready
- **AND** the `design` artifact remains blocked until specs exist

#### Scenario: Design unlocks mandatory review before ADR
- **GIVEN** proposal, specs, and design artifacts exist for a change
- **WHEN** no `review.md` exists
- **THEN** the `review` artifact is ready
- **AND** the `adr` artifact remains blocked until `review.md` exists

#### Scenario: ADR unlocks test plan before tasks
- **GIVEN** proposal, specs, design, review, and ADR artifacts exist for a change
- **WHEN** no `test-plan.md` exists
- **THEN** the `test-plan` artifact is ready
- **AND** the `tasks` artifact remains blocked until `test-plan.md` exists

#### Scenario: Tasks require all planning gates
- **GIVEN** proposal, specs, design, review, ADR, and test-plan artifacts exist for a change
- **WHEN** Codex checks OpenSpec status
- **THEN** the `tasks` artifact can become ready

### Requirement: Per-change ADR artifact is tracked by OpenSpec
The schema MUST track ADR review with `openspec/changes/<change>/adr.md` after context-aware review while allowing durable ADR files to live in top-level `adr/`.

#### Scenario: ADR review gates test plan
- **GIVEN** proposal, specs, design, and review artifacts exist for a change
- **WHEN** Codex creates `openspec/changes/<change>/adr.md`
- **THEN** OpenSpec marks the `adr` artifact as complete
- **AND** the `test-plan` artifact can become ready

#### Scenario: Tasks remain blocked without ADR review
- **GIVEN** proposal, specs, design, review, and test-plan content are planned
- **WHEN** no per-change `adr.md` exists
- **THEN** the `tasks` artifact remains blocked

### Requirement: Schema guidance includes persistent project context
The `intent-driven` schema SHALL instruct Codex to account for root `CONSTITUTION.md`, root `ARCHITECTURE.md`, and in-force ADR context during artifact creation, review, test planning, and apply guidance without making those files OpenSpec change artifacts.

#### Scenario: Artifact instructions reference project context
- **GIVEN** Codex requests instructions for an intent-driven planning artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to consider relevant `CONSTITUTION.md`, `ARCHITECTURE.md`, and ADR rules or decisions
- **AND** the instruction preserves the artifact's existing OpenSpec output path

#### Scenario: Review instructions reference project context
- **GIVEN** Codex requests instructions for the `review` artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to read proposal, specs, design, constitution, architecture, ADRs, docs, and relevant code before recording review conclusions

#### Scenario: Test plan instructions reference verification context
- **GIVEN** Codex requests instructions for the `test-plan` artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to map requirements and scenarios to verification and to account for constitution verification rules, architecture, and ADR constraints

#### Scenario: Apply instructions reference project context
- **GIVEN** an intent-driven change is apply-ready
- **WHEN** Codex requests `openspec instructions apply --change <change> --json`
- **THEN** the apply instruction tells Codex to account for relevant constitution, architecture, and ADR context before implementation
- **AND** Codex still reads the OpenSpec context files returned by the CLI

### Requirement: Persistent context guidance does not alter schema lifecycle
The `intent-driven` schema MUST keep persistent project context outside the expanded OpenSpec artifact lifecycle while adding mandatory `review` and `test-plan` artifacts for development and enhancement changes.

#### Scenario: Lifecycle includes review and test plan but excludes project context files
- **GIVEN** a new change uses the `intent-driven` schema
- **WHEN** Codex checks `openspec status --change <change> --json`
- **THEN** the artifact order includes `proposal`, `specs`, `design`, `review`, `adr`, `test-plan`, and `tasks`
- **AND** `CONSTITUTION.md`, `ARCHITECTURE.md`, and `adr/*.md` do not appear as change artifacts

#### Scenario: Schema remains valid
- **GIVEN** the review and test-plan artifacts have been added to the schema
- **WHEN** Codex runs `openspec schema validate intent-driven`
- **THEN** OpenSpec reports the schema as valid

## ADDED Requirements

### Requirement: Review and test-plan templates are provided
The schema MUST provide OpenSpec-compatible templates for `review.md` and `test-plan.md` so new projects receive the mandatory gates out of the box.

#### Scenario: Review template is available
- **GIVEN** the intent-driven schema is installed
- **WHEN** Codex requests `openspec instructions review --change <change>`
- **THEN** OpenSpec returns a template for `openspec/changes/<change>/review.md`
- **AND** the template includes context reviewed, questions and resolutions, artifact updates required, and remaining risks

#### Scenario: Test plan template is available
- **GIVEN** the intent-driven schema is installed
- **WHEN** Codex requests `openspec instructions test-plan --change <change>`
- **THEN** OpenSpec returns a template for `openspec/changes/<change>/test-plan.md`
- **AND** the template includes verification strategy, test-first scope, required checks, requirement mapping, and limitations
