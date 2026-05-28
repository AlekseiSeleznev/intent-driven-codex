## ADDED Requirements

### Requirement: Opsx continue and fast-forward execute mandatory grill gates
The `/opsx:continue`, `/opsx:propose`, and `/opsx:ff` workflows MUST execute the `grill-with-docs` gate automatically when `grill` or `design-review` is the next ready artifact.

#### Scenario: Continue reaches grill
- **GIVEN** proposal and specs are complete for a change
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex creates `grill.md` using `grill-with-docs`
- **AND** Codex does not ask whether to run grill

#### Scenario: Continue reaches design review
- **GIVEN** design is complete for a change
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex creates `design-review.md` using `grill-with-docs`
- **AND** Codex blocks ADR planning if unresolved material questions remain

### Requirement: Opsx apply follows canonical TDD without asking
The `/opsx:apply` workflow MUST apply the vendored `tdd` skill for behavior-changing implementation tasks without asking whether to use TDD.

#### Scenario: Apply reads TDD plan before implementation
- **GIVEN** a change is ready for apply
- **WHEN** Codex starts implementation
- **THEN** Codex reads `test-plan.md` and the vendored `tdd` skill guidance before modifying production code

#### Scenario: Apply marks tasks only after slice evidence
- **GIVEN** a task belongs to a TDD slice
- **WHEN** Codex completes the task
- **THEN** Codex marks it complete only after RED and GREEN evidence have been produced or an approved exception is recorded
