## MODIFIED Requirements

### Requirement: Every lifecycle step ends with a git checkpoint
The workflow MUST require a git checkpoint after each OpenSpec lifecycle state change before a dependent lifecycle phase proceeds, including `review.md` and `test-plan.md` gates.

#### Scenario: Proposal checkpoint gates specs and design
- **GIVEN** Codex has created or changed `proposal.md`
- **WHEN** the user asks Codex to create a dependent artifact
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stops unless the checkpoint is completed or the user explicitly overrides the gate

#### Scenario: Review checkpoint gates ADR
- **GIVEN** Codex has created or changed `review.md`
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

### Requirement: Apply and archive have hard gates
The workflow MUST block apply and archive when required git and OpenSpec states are not satisfied.

#### Scenario: Apply is blocked by uncommitted planning artifacts
- **GIVEN** proposal, specs, design, review, ADR review, test plan, or tasks have uncommitted changes
- **WHEN** the user invokes `/opsx:apply <change>`
- **THEN** Codex refuses to apply
- **AND** Codex explains which planning files need a checkpoint

#### Scenario: Archive is blocked before merge to base branch
- **GIVEN** implementation exists only on a feature branch or worktree
- **WHEN** the user invokes `/opsx:archive <change>`
- **THEN** Codex refuses to archive until the implementation is merged into the chosen base branch
