## MODIFIED Requirements

### Requirement: Every lifecycle step ends with a git checkpoint
The workflow MUST require a git checkpoint after each OpenSpec lifecycle state change before a dependent lifecycle phase proceeds, including `grill.md`, `design-review.md`, `test-plan.md` gates, and any tracked Claude artifact review report generated for that lifecycle step. For normal scaffold, artifact, and coherent apply task-group checkpoints, the workflow SHALL create the checkpoint automatically by default when session git discipline is in automatic mode, while still allowing the user to request manual checkpoint approval or a one-time override before the next dependent phase.

#### Scenario: Automatic proposal checkpoint gates specs and grill
- **GIVEN** Codex has created or changed `proposal.md`
- **AND** session git discipline is in automatic mode
- **WHEN** the user asks Codex to create a dependent artifact
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stages the relevant files and creates the checkpoint commit before the dependent artifact is created
- **AND** Codex reports the commit hash after the checkpoint succeeds

#### Scenario: Manual proposal checkpoint gates specs and grill
- **GIVEN** Codex has created or changed `proposal.md`
- **AND** the user has requested manual git checkpoint approval for the session
- **WHEN** the user asks Codex to create a dependent artifact
- **THEN** Codex shows the changed files and proposed commit message
- **AND** Codex stops unless the checkpoint is completed or the user explicitly overrides the gate

#### Scenario: Grill checkpoint gates design
- **GIVEN** Codex has created or changed `grill.md`
- **WHEN** the user asks Codex to create `design.md`
- **THEN** Codex checkpoints the `grill.md` lifecycle state according to the current session git discipline mode
- **AND** Codex does not let `design.md` consume uncheckpointed grill output unless the user explicitly overrides the gate

#### Scenario: Design review checkpoint gates ADR
- **GIVEN** Codex has created or changed `design-review.md`
- **WHEN** the user asks Codex to create `adr.md`
- **THEN** Codex checkpoints the `design-review.md` lifecycle state according to the current session git discipline mode
- **AND** Codex does not let `adr.md` consume uncheckpointed design-review output unless the user explicitly overrides the gate

#### Scenario: Test-plan checkpoint gates tasks
- **GIVEN** Codex has created or changed `test-plan.md`
- **WHEN** the user asks Codex to create `tasks.md`
- **THEN** Codex checkpoints the `test-plan.md` lifecycle state according to the current session git discipline mode
- **AND** Codex does not let `tasks.md` consume uncheckpointed test-plan output unless the user explicitly overrides the gate

#### Scenario: Tasks checkpoint gates apply
- **GIVEN** Codex has created `tasks.md`
- **WHEN** the user asks Codex to apply the change
- **THEN** Codex verifies the planning artifacts are committed and integrated into the chosen base branch

#### Scenario: Review report checkpoint gates dependent artifact
- **GIVEN** Claude artifact review generated a tracked report for `design.md`
- **WHEN** Codex prepares to create `design-review.md`
- **THEN** Codex includes the review report in the checkpoint state for the design step
- **AND** Codex does not let `design-review.md` consume uncheckpointed design review output unless the user explicitly overrides the checkpoint gate

### Requirement: Apply and archive have hard gates
The workflow MUST block apply and archive when required git and OpenSpec states are not satisfied, regardless of whether ordinary lifecycle checkpoints are automatic or manual. Automatic git discipline SHALL NOT bypass dirty unrelated work, project-rule conflicts, branch/merge requirements, incomplete planning artifacts, missing verification evidence, or archive integration requirements.

#### Scenario: Apply is blocked by uncommitted planning artifacts
- **GIVEN** proposal, specs, grill, design, design-review, ADR review, test plan, or tasks have uncommitted changes
- **WHEN** the user invokes `/opsx:apply <change>`
- **THEN** Codex refuses to apply
- **AND** Codex explains which planning files need a checkpoint

#### Scenario: Archive is blocked before merge to base branch
- **GIVEN** implementation exists only on a feature branch or worktree
- **WHEN** the user invokes `/opsx:archive <change>`
- **THEN** Codex refuses to archive until the implementation is merged into the chosen base branch

#### Scenario: Dirty unrelated work blocks automatic checkpointing
- **GIVEN** a lifecycle artifact is ready to checkpoint automatically
- **AND** `git status --short` includes unrelated modified, deleted, or untracked files that are not part of the current lifecycle step
- **WHEN** Codex prepares the checkpoint
- **THEN** Codex stops before staging files
- **AND** Codex reports the unrelated paths that must be committed, stashed, ignored, or explicitly trusted by a one-time override

#### Scenario: One-time override records accepted risk
- **GIVEN** a user explicitly bypasses a git checkpoint or hard gate
- **WHEN** Codex continues after the override
- **THEN** Codex records the gate bypassed, trusted files or state, accepted risk, and next checkpoint where normal discipline resumes
- **AND** Codex treats the override as expired after that continuation

### Requirement: Codex does not hide mutating git actions
The workflow SHALL make every mutating git action visible to the user. Safe lifecycle checkpoint commits MAY run automatically in automatic mode, but Codex MUST still show the affected paths, commit message, and resulting commit hash; branch creation, merges, pushes, pull requests, archive operations, destructive git operations, and manual-mode commits SHALL still require explicit user approval.

#### Scenario: Automatic checkpoint commit is visible
- **GIVEN** session git discipline is in automatic mode
- **AND** a lifecycle step has produced only files belonging to that step
- **WHEN** Codex creates the checkpoint commit
- **THEN** Codex shows the staged paths and commit message before committing
- **AND** Codex shows the resulting commit hash and clean or remaining `git status --short` after committing

#### Scenario: User approves manual commit
- **GIVEN** a lifecycle step has produced file changes
- **AND** session git discipline is in manual mode
- **WHEN** the user approves the checkpoint
- **THEN** Codex stages the relevant files and creates the commit

#### Scenario: User declines manual commit
- **GIVEN** a lifecycle step has produced file changes
- **AND** session git discipline is in manual mode
- **WHEN** the user declines the checkpoint
- **THEN** Codex stops before the next dependent lifecycle phase

#### Scenario: Push still requires explicit approval
- **GIVEN** automatic git discipline is enabled for lifecycle checkpoints
- **WHEN** Codex prepares to push commits, create a pull request, merge branches, or run a destructive git operation
- **THEN** Codex asks for explicit approval before taking that action
- **AND** Codex does not treat automatic checkpoint mode as approval for that action
