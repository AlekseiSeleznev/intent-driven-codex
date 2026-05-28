# context-aware-review Specification

## Purpose
Defines context-aware review, scenario quality, C4 diagram guidance, and use of persistent project context during review.
## Requirements
### Requirement: Proposal review uses grill-with-docs
The workflow SHALL use `grill-with-docs` instead of plain `grill-me` as a mandatory context-aware `review.md` gate for every development or enhancement change before ADR and task planning.

#### Scenario: Review reads available context
- **GIVEN** proposal, specs, and design artifacts exist for a change
- **WHEN** Codex creates `review.md`
- **THEN** Codex reads relevant OpenSpec artifacts, `CONSTITUTION.md`, `ARCHITECTURE.md`, project documents, ADRs, and code before asking or recording review questions

#### Scenario: Review asks focused questions
- **GIVEN** the review identifies multiple uncertainties
- **WHEN** Codex asks the user for clarification
- **THEN** Codex asks one focused question at a time
- **AND** Codex includes its recommended answer and rationale

#### Scenario: Low-risk review remains concise
- **GIVEN** Codex reviews a small development or enhancement change
- **AND** relevant documents and code reveal no material uncertainty
- **WHEN** Codex creates `review.md`
- **THEN** the review records that no material questions remain
- **AND** the workflow does not ask ceremonial questions only to satisfy the gate

### Requirement: Specs use Gherkin-style observable scenarios
The workflow MUST apply Gherkin authoring guidance to OpenSpec Markdown specs so scenarios describe domain behavior and observable outcomes.

#### Scenario: Scenario uses domain language
- **GIVEN** Codex drafts a requirement scenario
- **WHEN** the behavior is user-observable
- **THEN** the scenario describes initial state, event, and outcome in domain terms
- **AND** it avoids private implementation details unless they are part of the contract

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

### Requirement: Design review can re-run with docs
The workflow SHALL use `grill-with-docs` to review `design.md` before ADR and tasks for development or enhancement changes and SHALL integrate material review results into the affected OpenSpec artifacts before proceeding.

#### Scenario: Design review proposes artifact updates
- **GIVEN** design review finds a contradiction with specs or ADRs
- **WHEN** Codex reports the review result
- **THEN** Codex proposes exact updates to the affected artifacts before tasks are created

#### Scenario: Review resolutions are integrated
- **GIVEN** `review.md` records a material accepted resolution
- **WHEN** the resolution changes scope, behavior, technical approach, or durable decision planning
- **THEN** Codex updates the relevant proposal, specs, design, or ADR planning artifact
- **AND** `review.md` identifies where the resolution was integrated

#### Scenario: ADR remains blocked by unresolved review
- **GIVEN** `review.md` contains unresolved material questions or unchecked artifact updates required
- **WHEN** Codex prepares the ADR artifact
- **THEN** Codex stops and resolves or explicitly defers those review findings before creating `adr.md`
