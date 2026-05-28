## MODIFIED Requirements

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
