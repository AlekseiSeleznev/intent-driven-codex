## ADDED Requirements

### Requirement: Codex exposes OpenSpec workflow commands
The template SHALL provide Codex-native `/opsx:*` prompts for starting, continuing, applying, verifying, syncing, archiving, and checking intent-driven OpenSpec changes.

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

### Requirement: Codex apply reads all implementation context
The `/opsx:apply` workflow MUST read every OpenSpec context file and durable ADR constraints before implementing tasks.

#### Scenario: Apply includes top-level ADRs
- **GIVEN** a change is ready for apply
- **AND** the repository contains top-level `adr/*.md` files
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex reads the files returned by `openspec instructions apply --json`
- **AND** Codex also reads currently in-force top-level ADR files referenced by the ADR review

#### Scenario: Apply pauses on artifact mismatch
- **GIVEN** a task conflicts with the proposal, specs, design, or ADR review
- **WHEN** Codex detects the mismatch during `/opsx:apply`
- **THEN** Codex pauses implementation
- **AND** Codex recommends updating the relevant artifact before continuing

### Requirement: Codex verification compares implementation to artifacts
The `/opsx:verify` workflow SHALL verify task completion, requirement coverage, design coherence, and ADR compliance before archive.

#### Scenario: Verification blocks archive on incomplete tasks
- **GIVEN** a change has unchecked tasks in `tasks.md`
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the verification report lists incomplete tasks as critical issues
- **AND** Codex does not recommend archive

#### Scenario: Verification reports requirement evidence
- **GIVEN** a change has delta specs and implementation changes
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the report maps requirements and scenarios to implementation or test evidence where possible

### Requirement: Bulk apply is isolated and optional
The template MAY provide `/opsx:bulk-apply` for multiple independent changes, and when present it MUST use isolated worktrees and must not merge or archive automatically.

#### Scenario: Bulk apply creates isolated worktrees
- **GIVEN** two or more active changes are eligible for apply
- **WHEN** the user invokes `/opsx:bulk-apply`
- **THEN** Codex creates one isolated worktree per executed change
- **AND** each delegated flow runs apply and verify before reporting

#### Scenario: Bulk apply requires explicit merge approval
- **GIVEN** a bulk apply run finishes successfully
- **WHEN** Codex reports the results
- **THEN** no merge or archive has been performed
- **AND** Codex asks for explicit follow-up approval before any merge or archive
