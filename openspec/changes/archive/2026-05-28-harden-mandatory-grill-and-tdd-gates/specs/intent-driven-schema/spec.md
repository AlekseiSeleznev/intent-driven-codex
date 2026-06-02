## ADDED Requirements

### Requirement: Lifecycle uses mandatory Matt grill gates
The `intent-driven` schema MUST expose the planning lifecycle `proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks` for new development and enhancement changes.

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

#### Scenario: Apply context includes both grill gates
- **GIVEN** a change is ready for apply
- **WHEN** Codex requests `openspec instructions apply --change <change> --json`
- **THEN** the returned context files include `grill.md`, `design.md`, `design-review.md`, `adr.md`, `test-plan.md`, `tasks.md`, proposal, and specs

### Requirement: Templates enforce non-empty grill and TDD evidence
The schema MUST provide templates that require material grill outcomes and vertical TDD evidence rather than default `None` placeholders.

#### Scenario: Grill template records resolved uncertainty
- **GIVEN** Codex requests instructions for the `grill` artifact
- **WHEN** OpenSpec returns the template
- **THEN** the template includes context read, question loop, resolved terms, document updates, ADR candidates, and open questions
- **AND** it requires `Open Questions: None` before design may proceed

#### Scenario: Test plan template records vertical TDD slices
- **GIVEN** Codex requests instructions for the `test-plan` artifact
- **WHEN** OpenSpec returns the template
- **THEN** the template includes behavior slices with RED command/evidence, GREEN command/evidence, refactor criteria, public interface, and mock boundaries
- **AND** it does not contain a default `None` verification scope
