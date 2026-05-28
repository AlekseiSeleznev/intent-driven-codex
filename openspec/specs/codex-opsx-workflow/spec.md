# codex-opsx-workflow Specification

## Purpose
Defines Codex `/opsx:*` workflow commands, apply/verify/bulk behavior, Goal hand-off guidance, project-context preflight, grill gates, and TDD behavior.

## Requirements

### Requirement: Codex exposes OpenSpec workflow commands
The template SHALL provide Codex-native `/opsx:*` prompts for starting, continuing, applying, verifying, syncing, archiving, and checking intent-driven OpenSpec changes, including mandatory grill, design-review, and test-plan artifacts in the active lifecycle.

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

### Requirement: Codex apply reads all implementation context
The `/opsx:apply` workflow MUST read every OpenSpec context file, grill findings, test-plan requirements, glossary context, and durable ADR constraints before implementing tasks.

#### Scenario: Apply includes top-level ADRs and glossary
- **GIVEN** a change is ready for apply
- **AND** the repository contains top-level `adr/*.md` files or glossary context
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex reads the files returned by `openspec instructions apply --json`
- **AND** Codex also reads relevant `CONTEXT.md` or `CONTEXT-MAP.md` entries and currently in-force top-level ADR files

#### Scenario: Apply includes grill gates and test plan
- **GIVEN** a change is ready for apply
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex reads `grill.md`, `design-review.md`, and `test-plan.md` from the OpenSpec context files
- **AND** Codex uses grill resolutions and TDD slices when ordering implementation work

#### Scenario: Apply pauses on artifact mismatch
- **GIVEN** a task conflicts with the proposal, specs, grill, design, design-review, ADR review, or test plan
- **WHEN** Codex detects the mismatch during `/opsx:apply`
- **THEN** Codex pauses implementation
- **AND** Codex recommends updating the relevant artifact before continuing

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

### Requirement: Codex verification compares implementation to artifacts
The `/opsx:verify` workflow SHALL verify task completion, requirement coverage, design coherence, grill/design-review resolution integration, ADR compliance, and RED/GREEN test-plan evidence before archive.

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

### Requirement: Apply can hand off long work to Codex Goal
The `/opsx:apply` workflow SHALL evaluate whether an apply run should be goal-guided before implementation, and when goal guidance is needed it MUST stop before implementation and present a ready-to-copy `/goal` prompt for the current change.

#### Scenario: Long apply receives a generated goal prompt
- **GIVEN** an OpenSpec change is ready for apply
- **AND** the apply work has multiple pending tasks, material ADR/design context, expected checkpoints, or other long-running risk
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex presents a copy-paste prompt that starts with `/goal`
- **AND** Codex stops before modifying implementation files

#### Scenario: Small apply can continue without goal guidance
- **GIVEN** an OpenSpec change is ready for apply
- **AND** the apply work is small, local, and unlikely to cross multiple checkpoint boundaries
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex may continue the normal apply workflow without generating a `/goal` prompt
- **AND** Codex still follows OpenSpec context, TDD, verification, and git checkpoint rules

### Requirement: Opsx workflows run constitution preflight
All `/opsx:*` workflows SHALL run a shared Codex-layer constitution preflight before taking workflow actions, while keeping OpenSpec CLI responsibility unchanged.

#### Scenario: Preflight reads constitution before workflow action
- **GIVEN** `CONSTITUTION.md` exists at the project root
- **WHEN** the user invokes an `/opsx:*` workflow
- **THEN** Codex reads the constitution before acting on OpenSpec state
- **AND** Codex applies relevant project rules to the requested workflow

#### Scenario: Missing constitution allows only bootstrap-safe actions
- **GIVEN** `CONSTITUTION.md` is missing
- **WHEN** the user invokes an `/opsx:*` workflow
- **THEN** Codex reports that the constitution is missing
- **AND** Codex allows only bootstrap-safe or diagnostic actions unless the user gives an explicit one-time override

### Requirement: Opsx workflows use external credentials only when needed
The constitution preflight MUST load local `.secrets.local.env` values only when the requested workflow needs access to an external system listed in `CONSTITUTION.md`, and MUST never print secret values.

#### Scenario: Workflow does not need external access
- **GIVEN** `CONSTITUTION.md` lists an external system with credential variables
- **AND** the requested workflow does not need that system
- **WHEN** Codex runs the constitution preflight
- **THEN** Codex does not need to read `.secrets.local.env` for that system
- **AND** Codex may continue using the non-secret constitution context

#### Scenario: Workflow needs external access with missing credentials
- **GIVEN** `CONSTITUTION.md` lists an external system required by the current workflow
- **AND** `.secrets.local.env` is missing or lacks required variables
- **WHEN** Codex prepares to use the external system
- **THEN** Codex stops before making the external-system call
- **AND** Codex reports the missing variable names without revealing any secret values
