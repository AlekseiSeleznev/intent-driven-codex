# intent-driven-schema Specification

## Purpose
TBD - created by archiving change implement-intent-driven-codex-template. Update Purpose after archive.
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

### Requirement: Artifact lifecycle follows intent-driven order
The `intent-driven` schema MUST guide changes through `proposal`, `specs`, `design`, `adr`, and `tasks` in that order before implementation is allowed.

#### Scenario: Proposal unlocks specs before design
- **GIVEN** a new change uses the `intent-driven` schema
- **WHEN** `proposal.md` exists and no spec files exist
- **THEN** the `specs` artifact is ready
- **AND** the `design` artifact remains blocked until specs exist

#### Scenario: Tasks require specs and ADR review
- **GIVEN** proposal, specs, and design artifacts exist for a change
- **WHEN** no per-change `adr.md` exists
- **THEN** the `tasks` artifact remains blocked

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

### Requirement: Per-change ADR artifact is tracked by OpenSpec
The schema MUST track ADR review with `openspec/changes/<change>/adr.md` while allowing durable ADR files to live in top-level `adr/`.

#### Scenario: ADR review gates tasks
- **GIVEN** proposal, specs, and design artifacts exist for a change
- **WHEN** Codex creates `openspec/changes/<change>/adr.md`
- **THEN** OpenSpec marks the `adr` artifact as complete
- **AND** the `tasks` artifact can become ready

