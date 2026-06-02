## MODIFIED Requirements

### Requirement: Proposal review uses grill-with-docs
The workflow SHALL use `grill-with-docs` instead of plain `grill-me` when proposal scope, risk, assumptions, Brownfield context, or project context architecture changes require review.

#### Scenario: Review reads available context
- **GIVEN** a proposal draft exists for a change
- **WHEN** Codex invokes proposal review
- **THEN** Codex reads relevant OpenSpec artifacts, `CONSTITUTION.md`, `ARCHITECTURE.md`, project documents, ADRs, and code before asking review questions

#### Scenario: Review asks focused questions
- **GIVEN** the review identifies multiple uncertainties
- **WHEN** Codex asks the user for clarification
- **THEN** Codex asks one focused question at a time
- **AND** Codex includes its recommended answer and rationale

### Requirement: Design uses C4 guidance when boundaries are non-trivial
The workflow SHALL use C4-style diagramming guidance when a change affects multiple components, services, integrations, runtime units, deployment boundaries, project context architecture, or durable architecture summaries.

#### Scenario: Design includes minimal useful C4 view
- **GIVEN** a change affects more than one runtime or architectural boundary
- **WHEN** Codex creates `design.md`
- **THEN** the design contains the smallest useful C4-style ASCII or Mermaid diagram
- **AND** assumptions and open questions are explicitly listed

#### Scenario: Simple change records no C4 needed
- **GIVEN** a change is local and has no meaningful architecture boundary
- **WHEN** Codex creates `design.md`
- **THEN** the design explicitly records that no C4 diagram is needed and why
