## MODIFIED Requirements

### Requirement: Codex exposes OpenSpec workflow commands
The template SHALL provide Codex-native `/opsx:*` prompts for starting, continuing, applying, verifying, syncing, archiving, and checking intent-driven OpenSpec changes, including mandatory grill, design-review, and test-plan artifacts in the active lifecycle. When Claude artifact review is enabled for a lifecycle stage, these workflows SHALL run or account for the configured review before a dependent stage consumes the reviewed artifact.

#### Scenario: User starts a new change
- **GIVEN** the template is installed in a repository
- **WHEN** the user invokes `/opsx:new add-example-feature`
- **THEN** Codex creates `openspec/changes/add-example-feature/` through OpenSpec
- **AND** Codex shows the first ready artifact instructions

#### Scenario: User continues a change
- **GIVEN** an active intent-driven change has one ready artifact
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex reads OpenSpec status and instructions for the ready artifact
- **AND** Codex creates only that artifact before stopping

#### Scenario: Continue reaches grill
- **GIVEN** proposal and specs are complete for a change
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex identifies `grill` as the next ready artifact
- **AND** Codex creates `grill.md` using `grill-with-docs` without asking whether to run it

#### Scenario: Continue reaches design review
- **GIVEN** proposal, specs, grill, and design artifacts are complete for a change
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex identifies `design-review` as the next ready artifact
- **AND** Codex creates `design-review.md` using `grill-with-docs` without asking whether to run it

#### Scenario: Continue runs enabled Claude review
- **GIVEN** `/opsx:continue <change>` has created an artifact
- **AND** Claude artifact review is enabled for that artifact's stage
- **WHEN** Codex reaches the post-artifact checkpoint boundary
- **THEN** Codex runs the configured Claude review or reports why it cannot run
- **AND** Codex applies the configured pass/warn/fail/blocked policy before creating the next dependent artifact

#### Scenario: Fast-forward honors review gates
- **GIVEN** `/opsx:ff <change>` or `/opsx:propose <change>` is creating multiple artifacts in sequence
- **AND** Claude artifact review is enabled for at least one intermediate stage
- **WHEN** Codex finishes an enabled stage
- **THEN** Codex records the review result before continuing to the next dependent artifact
- **AND** Codex stops the batch if the review policy blocks progress

### Requirement: Codex verification compares implementation to artifacts
The `/opsx:verify` workflow SHALL verify task completion, requirement coverage, design coherence, grill/design-review resolution integration, ADR compliance, RED/GREEN test-plan evidence, and configured Claude artifact review results before archive.

#### Scenario: Verification blocks archive on incomplete tasks
- **GIVEN** a change has unchecked tasks in `tasks.md`
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the verification report lists incomplete tasks as critical issues
- **AND** Codex does not recommend archive

#### Scenario: Verification reports grill and TDD evidence
- **GIVEN** a change has `grill.md`, `design-review.md`, and `test-plan.md`
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the report identifies unresolved grill items or missing RED/GREEN evidence as critical issues
- **AND** the report includes evidence for completed test-plan checks when available

#### Scenario: Verification reports Claude review blockers
- **GIVEN** Claude artifact review is enabled for one or more stages in a change
- **AND** a required review report is missing or has a blocking verdict
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the verification report lists the missing or blocking review as a critical issue
- **AND** Codex does not recommend archive until the review policy is satisfied or explicitly overridden
