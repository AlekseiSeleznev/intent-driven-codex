## MODIFIED Requirements

### Requirement: Every lifecycle step ends with a git checkpoint
The workflow MUST require a git checkpoint after each OpenSpec lifecycle state change before a dependent lifecycle phase proceeds, including `grill.md`, `design-review.md`, `test-plan.md` gates, and any tracked Claude artifact review report generated for that lifecycle step.

#### Scenario: Proposal checkpoint gates specs and grill
- **GIVEN** Codex has created or changed `proposal.md`
- **WHEN** the user asks Codex to create a dependent artifact
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stops unless the checkpoint is completed or the user explicitly overrides the gate

#### Scenario: Grill checkpoint gates design
- **GIVEN** Codex has created or changed `grill.md`
- **WHEN** the user asks Codex to create `design.md`
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stops unless the checkpoint is completed or the user explicitly overrides the gate

#### Scenario: Design review checkpoint gates ADR
- **GIVEN** Codex has created or changed `design-review.md`
- **WHEN** the user asks Codex to create `adr.md`
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stops unless the checkpoint is completed or the user explicitly overrides the gate

#### Scenario: Test-plan checkpoint gates tasks
- **GIVEN** Codex has created or changed `test-plan.md`
- **WHEN** the user asks Codex to create `tasks.md`
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stops unless the checkpoint is completed or the user explicitly overrides the gate

#### Scenario: Tasks checkpoint gates apply
- **GIVEN** Codex has created `tasks.md`
- **WHEN** the user asks Codex to apply the change
- **THEN** Codex verifies the planning artifacts are committed and integrated into the chosen base branch

#### Scenario: Review report checkpoint gates dependent artifact
- **GIVEN** Claude artifact review generated a tracked report for `design.md`
- **WHEN** Codex prepares to create `design-review.md`
- **THEN** Codex includes the review report in the checkpoint state for the design step
- **AND** Codex does not let `design-review.md` consume uncheckpointed design review output unless the user explicitly overrides the checkpoint gate
