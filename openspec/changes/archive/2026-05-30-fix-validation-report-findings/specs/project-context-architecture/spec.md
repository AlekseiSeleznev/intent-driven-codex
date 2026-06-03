## ADDED Requirements

### Requirement: Architecture and ADR documentation remains coherent
Project architecture and durable ADR index documentation MUST describe the same in-force ADR set and supersession relationships using clear, readable sentences.

#### Scenario: In-force ADR summaries match
- **GIVEN** `ARCHITECTURE.md` and `adr/README.md` both summarize in-force durable ADRs
- **WHEN** a user compares those summaries
- **THEN** both identify the same in-force ADR set
- **AND** superseded ADRs are described consistently

#### Scenario: ADR supersession wording is readable
- **GIVEN** an ADR summary mentions that ADR 0007 narrows ADR 0001
- **WHEN** a user reads the sentence
- **THEN** the sentence is grammatically coherent
- **AND** it does not contain pasted headings or punctuation artifacts that obscure the relationship

### Requirement: Lifecycle documentation mirrors schema dependencies
Lifecycle documentation SHALL list artifact dependencies consistently with the project-local `intent-driven` schema when presenting dependency tables.

#### Scenario: Design dependencies are complete
- **GIVEN** the `intent-driven` schema requires `proposal`, `specs`, and `grill` before `design`
- **WHEN** a user reads `docs/lifecycle.md`
- **THEN** the dependency table lists all three dependencies for `design`
- **AND** it does not imply that `grill` is the only prerequisite
