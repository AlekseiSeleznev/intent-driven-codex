# codex-opsx-workflow Specification

## Purpose
TBD - created by archiving change implement-intent-driven-codex-template. Update Purpose after archive.
## Requirements
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

### Requirement: Apply can hand off long work to Codex Goal
The `/opsx:apply` workflow SHALL evaluate whether an apply run should be goal-guided before implementation, and when goal guidance is needed it MUST stop before implementation and present a ready-to-copy `/goal` prompt for the current change.

#### Scenario: Long apply receives a generated goal prompt
- **GIVEN** an OpenSpec change is ready for apply
- **AND** the apply work has multiple pending tasks, material ADR/design context, expected checkpoints, or other long-running risk
- **AND** the current run is not already inside an active Codex Goal
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex presents a copy-paste prompt that starts with `/goal`
- **AND** the prompt names `<change>` and instructs the goal to run `/opsx:apply <change>` as its first workflow action
- **AND** the prompt includes a semantic fallback to `openspec-apply-change` if nested slash-command execution is not literal
- **AND** Codex stops before modifying implementation files

#### Scenario: Small apply can continue without goal guidance
- **GIVEN** an OpenSpec change is ready for apply
- **AND** the apply work is small, local, and unlikely to cross multiple checkpoint boundaries
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex may continue the normal apply workflow without generating a `/goal` prompt
- **AND** Codex still follows OpenSpec context, verification, and git checkpoint rules

#### Scenario: Apply does not create a nested goal while already goal-guided
- **GIVEN** Codex is already executing an active goal whose scope includes `<change>`
- **WHEN** the goal invokes or describes `/opsx:apply <change>`
- **THEN** Codex continues the apply workflow directly
- **AND** Codex does not generate a second `/goal` prompt for the same apply run

### Requirement: Generated apply goals define completion and stop conditions
A generated `/goal` prompt for `/opsx:apply` MUST define when the goal is complete and when Codex must stop without marking the goal complete.

#### Scenario: Apply goal completion requires verification
- **GIVEN** Codex generated a `/goal` prompt for `/opsx:apply <change>`
- **WHEN** the user runs that prompt
- **THEN** the prompt defines completion as all applicable tasks completed, `/opsx:verify <change>` completed without critical issues, and a final report produced
- **AND** the prompt states that archive, merge, push, or destructive git actions require separate explicit approval

#### Scenario: Apply goal stops on external or ambiguous blockers
- **GIVEN** Codex is executing a generated apply goal
- **WHEN** planning artifacts are dirty, OpenSpec reports a blocked state, required credentials are missing, an external service is unavailable, checks fail for reasons outside Codex control, artifacts conflict, or implementation requires a design/spec/ADR decision
- **THEN** Codex stops without marking the goal complete
- **AND** Codex reports the blocker, trusted state, and recommended next user action

### Requirement: Bulk apply can hand off orchestration to a parent Codex Goal
The `/opsx:bulk-apply` workflow SHALL provide a parent Codex Goal prompt for eligible multi-change runs before creating worktrees or dispatching subagents, unless goal guidance is already active or explicitly bypassed.

#### Scenario: Bulk apply receives a generated parent goal prompt
- **GIVEN** two or more active OpenSpec changes are eligible for bulk apply
- **AND** the current run is not already inside an active Codex Goal
- **WHEN** Codex starts `/opsx:bulk-apply <changes...>`
- **THEN** Codex presents a copy-paste prompt that starts with `/goal`
- **AND** the prompt names every target change and instructs the goal to run `/opsx:bulk-apply <changes...>` as its first workflow action
- **AND** the prompt includes a semantic fallback to `openspec-bulk-apply-change` if nested slash-command execution is not literal
- **AND** Codex stops before creating worktrees or dispatching subagents

#### Scenario: Bulk apply goal completion requires per-change verify and final report
- **GIVEN** the user runs a generated `/goal` prompt for `/opsx:bulk-apply <changes...>`
- **WHEN** Codex executes the goal
- **THEN** the goal is complete only after each executed change has an isolated worktree, apply result, verify result, changed-files summary, blocker summary, and a normalized parent report
- **AND** the goal does not merge, archive, push, or perform destructive git actions without separate explicit approval

#### Scenario: Bulk apply goal stops on orchestration blockers
- **GIVEN** Codex is executing a generated bulk apply goal
- **WHEN** fewer than two executable changes remain, a planning-artifact gate fails, worktree creation fails, a subagent cannot be dispatched, an external dependency is unavailable, or a merge/worktree conflict requires a human decision
- **THEN** Codex stops without marking the goal complete
- **AND** Codex reports which changes were skipped, paused, or failed and what user action is required
