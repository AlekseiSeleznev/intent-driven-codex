# intent-driven-schema Specification

## Purpose
Defines the project-local `intent-driven` schema and how schema instructions guide Codex through proposal, specs, grill, design, design-review, ADR review, test planning, tasks, apply, and persistent project context.
## Requirements
### Requirement: Intent-driven schema is project-local and defaultable
The system SHALL provide a project-local OpenSpec schema named `intent-driven` that can be selected as the default workflow for a repository.

#### Scenario: Schema is listed by OpenSpec
- **GIVEN** the template has been installed in a repository
- **WHEN** Codex runs `openspec schemas --json`
- **THEN** the response includes a project schema named `intent-driven`

#### Scenario: Schema validation succeeds
- **GIVEN** the template schema files are present
- **WHEN** Codex runs `openspec schema validate intent-driven`
- **THEN** OpenSpec reports the schema as valid

### Requirement: Artifact lifecycle follows hardened intent-driven order
The `intent-driven` schema MUST guide development and enhancement changes through `proposal`, `specs`, `grill`, `design`, `design-review`, `adr`, `test-plan`, and `tasks` in that order before implementation is allowed.

#### Scenario: Proposal unlocks specs before grill
- **GIVEN** a new change uses the `intent-driven` schema
- **WHEN** `proposal.md` exists and no spec files exist
- **THEN** the `specs` artifact is ready
- **AND** the `grill` artifact remains blocked until specs exist

#### Scenario: Grill is required before design
- **GIVEN** a change has completed `proposal.md` and delta specs
- **WHEN** Codex checks `openspec status --change <change> --json`
- **THEN** the `grill` artifact is ready
- **AND** the `design` artifact remains blocked until `grill.md` exists

#### Scenario: Design review is required before ADR
- **GIVEN** a change has completed proposal, specs, grill, and design artifacts
- **WHEN** Codex checks OpenSpec status
- **THEN** the `design-review` artifact is ready
- **AND** the `adr` artifact remains blocked until `design-review.md` exists

#### Scenario: Tasks require all planning gates
- **GIVEN** proposal, specs, grill, design, design-review, ADR, and test-plan artifacts exist for a change
- **WHEN** Codex checks OpenSpec status
- **THEN** the `tasks` artifact can become ready

### Requirement: Specification templates are OpenSpec-compatible and behavior-focused
The schema MUST generate Markdown spec templates that include normative requirement text and observable Gherkin-style scenarios compatible with OpenSpec validation and archive.

#### Scenario: Generated requirement includes normative text
- **GIVEN** Codex creates a delta spec from the `intent-driven` template
- **WHEN** the spec is validated in strict mode
- **THEN** each requirement contains `MUST` or `SHALL` normative behavior text before its scenarios

#### Scenario: Generated scenarios use OpenSpec headings
- **GIVEN** Codex creates a delta spec from the `intent-driven` template
- **WHEN** OpenSpec parses the spec
- **THEN** each scenario uses a `#### Scenario:` heading

### Requirement: Schema guidance includes persistent project context
The `intent-driven` schema SHALL instruct Codex to account for root `CONSTITUTION.md`, root `CONTEXT.md` or `CONTEXT-MAP.md`, root `ARCHITECTURE.md`, in-force ADR context, configured non-secret artifact review reports, and effective session-level workflow overlays during artifact creation, grill, test planning, verification, and apply guidance without making root project context files or session overlays OpenSpec change artifacts.

#### Scenario: Artifact instructions reference project context
- **GIVEN** Codex requests instructions for an intent-driven planning artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to consider relevant constitution, glossary, architecture, and ADR rules or decisions
- **AND** the instruction preserves the artifact's existing OpenSpec output path

#### Scenario: Apply instructions reference hardened context
- **GIVEN** an intent-driven change is apply-ready
- **WHEN** Codex requests `openspec instructions apply --change <change> --json`
- **THEN** the returned context files include proposal, specs, grill, design, design-review, ADR, test-plan, and tasks
- **AND** the apply instruction tells Codex to use the project-local `tdd` skill for behavior-changing implementation

#### Scenario: Review reports remain auxiliary context
- **GIVEN** Claude artifact review is enabled and has produced tracked review reports for a change
- **WHEN** Codex creates later artifacts, applies, or verifies the change
- **THEN** Codex treats those review reports as auxiliary reviewer context
- **AND** Codex does not treat the reports as replacements for proposal, specs, grill, design, design-review, ADR, test-plan, tasks, or OpenSpec validation

#### Scenario: Session overlays remain Codex-layer context
- **GIVEN** a session overlay configures automatic git checkpointing or Claude review settings
- **WHEN** Codex creates or consumes intent-driven artifacts
- **THEN** Codex uses the effective session overlay as workflow context
- **AND** Codex does not write the session overlay into OpenSpec lifecycle artifacts unless the artifact intentionally records a decision, override, or verification outcome

### Requirement: Hardened templates are provided
The schema MUST provide OpenSpec-compatible templates for `grill.md`, `design-review.md`, and `test-plan.md` so new projects receive the mandatory gates out of the box.

#### Scenario: Grill templates are available
- **GIVEN** the intent-driven schema is installed
- **WHEN** Codex requests `openspec instructions grill --change <change>` or `openspec instructions design-review --change <change>`
- **THEN** OpenSpec returns templates with context read, question loop, document updates, ADR candidates, and open questions

#### Scenario: Test plan template records vertical TDD slices
- **GIVEN** the intent-driven schema is installed
- **WHEN** Codex requests `openspec instructions test-plan --change <change>`
- **THEN** the template includes behavior slices with RED command/evidence, GREEN command/evidence, refactor criteria, public interface, and mock boundaries
- **AND** it does not contain a default `None` verification scope

### Requirement: Repository default config matches generated intent-driven config
The repository's tracked `openspec/config.yaml` MUST remain at least as complete as the intent-driven configuration generated by `scripts/install-overlay`, including hardened lifecycle rules and project context guidance.

#### Scenario: Repository config includes hardened artifact rules
- **GIVEN** the installer generates default rules for `grill`, `design-review`, `test-plan`, and `tasks`
- **WHEN** Codex compares the repository's tracked `openspec/config.yaml` with the installer-generated configuration
- **THEN** the repository config contains equivalent guidance for those hardened lifecycle artifacts
- **AND** the repository does not rely on a less complete config than newly installed projects receive

#### Scenario: Repository config includes project context guidance
- **GIVEN** installed projects receive `context` guidance for constitution, glossary, architecture, ADRs, and local secrets
- **WHEN** Codex reads the repository's tracked `openspec/config.yaml`
- **THEN** the same categories of context guidance are present
- **AND** the guidance remains non-secret and references variable names or local files only
