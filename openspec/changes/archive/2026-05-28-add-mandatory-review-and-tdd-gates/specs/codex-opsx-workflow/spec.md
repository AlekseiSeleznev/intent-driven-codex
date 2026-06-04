## MODIFIED Requirements

### Requirement: Codex exposes OpenSpec workflow commands
The template SHALL provide Codex-native `/opsx:*` prompts for starting, continuing, applying, verifying, syncing, archiving, and checking intent-driven OpenSpec changes, including the mandatory review and test-plan artifacts in the active lifecycle.

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

#### Scenario: Continue reaches review after design
- **GIVEN** proposal, specs, and design artifacts are complete for a change
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex identifies `review` as the next ready artifact
- **AND** Codex uses `grill-with-docs` guidance to create `review.md`

#### Scenario: Continue reaches test plan after ADR
- **GIVEN** proposal, specs, design, review, and ADR artifacts are complete for a change
- **WHEN** the user invokes `/opsx:continue <change>`
- **THEN** Codex identifies `test-plan` as the next ready artifact
- **AND** Codex creates `test-plan.md` before tasks are allowed

### Requirement: Codex apply reads all implementation context
The `/opsx:apply` workflow MUST read every OpenSpec context file, review findings, test-plan requirements, and durable ADR constraints before implementing tasks.

#### Scenario: Apply includes top-level ADRs
- **GIVEN** a change is ready for apply
- **AND** the repository contains top-level `adr/*.md` files
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex reads the files returned by `openspec instructions apply --json`
- **AND** Codex also reads currently in-force top-level ADR files referenced by the ADR review

#### Scenario: Apply includes review and test plan
- **GIVEN** a change is ready for apply
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex reads `review.md` and `test-plan.md` from the OpenSpec context files
- **AND** Codex uses review resolutions and test-plan checks when ordering implementation work

#### Scenario: Apply pauses on artifact mismatch
- **GIVEN** a task conflicts with the proposal, specs, design, review, ADR review, or test plan
- **WHEN** Codex detects the mismatch during `/opsx:apply`
- **THEN** Codex pauses implementation
- **AND** Codex recommends updating the relevant artifact before continuing

### Requirement: Codex verification compares implementation to artifacts
The `/opsx:verify` workflow SHALL verify task completion, requirement coverage, design coherence, review resolution integration, ADR compliance, and test-plan evidence before archive.

#### Scenario: Verification blocks archive on incomplete tasks
- **GIVEN** a change has unchecked tasks in `tasks.md`
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the verification report lists incomplete tasks as critical issues
- **AND** Codex does not recommend archive

#### Scenario: Verification reports requirement evidence
- **GIVEN** a change has delta specs and implementation changes
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the report maps requirements and scenarios to implementation or test evidence where possible

#### Scenario: Verification reports review and test-plan evidence
- **GIVEN** a change has `review.md` and `test-plan.md`
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the report identifies unresolved review items or missing test-plan checks as critical issues
- **AND** the report includes evidence for completed test-plan checks when available
