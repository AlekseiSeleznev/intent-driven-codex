# codex-opsx-workflow Specification

## Purpose
Defines Codex `/opsx:*` workflow commands, apply/verify/bulk behavior, Goal hand-off guidance, project-context preflight, grill gates, and TDD behavior.
## Requirements
### Requirement: Codex exposes OpenSpec workflow commands
The template SHALL provide Codex-native `/opsx:*` prompts for starting, continuing, applying, verifying, syncing, archiving, and checking intent-driven OpenSpec changes, including mandatory grill, design-review, and test-plan artifacts in the active lifecycle. When Claude artifact review is enabled for a lifecycle stage, these workflows SHALL run or account for the configured review before a dependent stage consumes the reviewed artifact, and key entry points SHALL initialize or reuse explicit session-level Claude review intent before running multi-stage workflows.

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

#### Scenario: Entry point asks for Claude review session intent
- **GIVEN** no Claude review session decision is recorded for the current Codex/OpenSpec session
- **WHEN** the user invokes `/opsx:new`, `/opsx:propose`, `/opsx:ff`, or `/opsx:apply`
- **THEN** Codex asks whether Claude artifact review should be enabled for the session before creating or consuming lifecycle artifacts
- **AND** Codex records the answer as a session overlay instead of editing the project-global review config

#### Scenario: Entry point reuses recorded Claude review intent
- **GIVEN** a Claude review session decision is already recorded for the current Codex/OpenSpec session
- **WHEN** the user invokes `/opsx:new`, `/opsx:propose`, `/opsx:ff`, `/opsx:apply`, or `/opsx:continue`
- **THEN** Codex uses the recorded session overlay without asking the same enablement question again
- **AND** Codex still allows the user to change the setting through the Claude review control commands

#### Scenario: Continue runs enabled Claude review
- **GIVEN** `/opsx:continue <change>` has created an artifact
- **AND** Claude artifact review is enabled for that artifact's stage after resolving project-global config and session overlay settings
- **WHEN** Codex reaches the post-artifact checkpoint boundary
- **THEN** Codex runs the configured Claude review or reports why it cannot run
- **AND** Codex applies the configured pass/warn/fail/blocked policy before creating the next dependent artifact

#### Scenario: Fast-forward honors review gates
- **GIVEN** `/opsx:ff <change>` or `/opsx:propose <change>` is creating multiple artifacts in sequence
- **AND** Claude artifact review is enabled for at least one intermediate stage after resolving project-global config and session overlay settings
- **WHEN** Codex finishes an enabled stage
- **THEN** Codex records the review result before continuing to the next dependent artifact
- **AND** Codex stops the batch if the review policy blocks progress

### Requirement: Codex apply reads all implementation context
The `/opsx:apply` workflow MUST read every OpenSpec context file, grill findings, test-plan requirements, glossary context, durable ADR constraints, and relevant Claude review session/report context before implementing tasks.

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

#### Scenario: Apply includes Claude review session context
- **GIVEN** Claude artifact review is enabled for the current session or review reports exist for the change
- **WHEN** Codex starts `/opsx:apply <change>`
- **THEN** Codex reads the effective non-secret session overlay and relevant review reports as auxiliary context
- **AND** Codex does not treat review reports as replacements for OpenSpec artifacts, grill findings, ADR review, or TDD evidence

#### Scenario: Apply pauses on artifact mismatch
- **GIVEN** a task conflicts with the proposal, specs, grill, design, design-review, ADR review, test plan, or blocking Claude review finding
- **WHEN** Codex detects the mismatch during `/opsx:apply`
- **THEN** Codex pauses implementation
- **AND** Codex recommends updating the relevant artifact, review setting, or implementation plan before continuing

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

### Requirement: Opsx exposes Claude review session commands
The template MUST provide Codex-native `/opsx:*` commands for showing, enabling, disabling, and updating Claude artifact review session settings without requiring users to edit the project-global review configuration by hand.

#### Scenario: Status command shows effective review settings
- **GIVEN** a project has `.codex/openspec-claude-review.json`
- **WHEN** the user invokes `/opsx:claude-review-status`
- **THEN** Codex reports whether review is enabled for the current session
- **AND** Codex shows effective stage settings after applying session overlays while omitting any secret values

#### Scenario: Enable command turns on session review
- **GIVEN** Claude artifact review is globally disabled in the tracked project config
- **WHEN** the user invokes `/opsx:claude-review-on`
- **THEN** Codex records a session overlay that enables review for the current session
- **AND** Codex leaves `.codex/openspec-claude-review.json` unchanged unless the user explicitly asks to change global defaults

#### Scenario: Disable command turns off session review
- **GIVEN** Claude artifact review is enabled by global config or session overlay
- **WHEN** the user invokes `/opsx:claude-review-off`
- **THEN** Codex records a session overlay that disables review for the current session
- **AND** later `/opsx:*` lifecycle commands skip Claude review because of that session decision

#### Scenario: Set command updates stage model and effort
- **GIVEN** a user wants different review depth for a lifecycle stage
- **WHEN** the user invokes `/opsx:claude-review-set --stage design-review --model claude-opus-4-8 --effort xhigh`
- **THEN** Codex validates the model and effort using the Claude review capability rules
- **AND** Codex records the setting in the session overlay if validation succeeds

### Requirement: Opsx apply reports aggregate Claude reviewer cost
The `/opsx:apply` workflow SHALL summarize total Claude artifact reviewer cost for the apply run or change when Claude review calls have produced cost metadata, while preserving existing apply, TDD, checkpoint, and review blocking behavior.

#### Scenario: Apply final summary includes total Claude cost
- **GIVEN** `/opsx:apply` invokes Claude artifact review one or more times during the apply run
- **AND** the reviewer results include `total_cost_usd` values
- **WHEN** Codex completes the apply workflow summary
- **THEN** Codex reports the aggregate Claude review cost for the apply run
- **AND** Codex identifies that the total covers Claude artifact reviewer calls, not unrelated Codex or OpenSpec CLI execution

#### Scenario: Apply can summarize persisted review report costs
- **GIVEN** a change has persisted Claude review reports under `openspec/changes/<change>/reviews/`
- **AND** those reports include cost metadata
- **WHEN** Codex needs a final Claude review cost summary for `/opsx:apply`
- **THEN** Codex can compute the aggregate from the persisted reports without invoking Claude again
- **AND** the summary excludes reports that do not contain numeric cost values

#### Scenario: Cost summary does not replace review gating
- **GIVEN** a Claude review report has a blocking verdict or a budget-exhaustion result
- **WHEN** `/opsx:apply` prepares its summary
- **THEN** Codex still applies the configured blocker behavior
- **AND** the cost summary is informational and does not override the blocker

### Requirement: Opsx command documentation covers shipped commands
The README command documentation SHALL list or cross-reference all shipped `/opsx:*` prompts that users need for the current workflow, including Claude review session controls.

#### Scenario: Claude review commands appear in command docs
- **GIVEN** the template ships `/opsx:claude-review-status`, `/opsx:claude-review-on`, `/opsx:claude-review-off`, `/opsx:claude-review-reset`, and `/opsx:claude-review-set`
- **WHEN** a user reads the main command table or its immediate cross-reference
- **THEN** those commands are discoverable from the command documentation
- **AND** the documentation says they change session overlays rather than project-global defaults unless explicitly requested

### Requirement: Support skill names are documented consistently
Workflow documentation MUST use installed skill identifiers consistently when naming support skills that Codex can invoke.

#### Scenario: Diagram skill uses canonical name
- **GIVEN** the installed support skill is named `c4-diagrams`
- **WHEN** README or lifecycle documentation refers to that support skill
- **THEN** it uses `c4-diagrams` or clearly labels any human-friendly name as referring to `c4-diagrams`

### Requirement: Opsx workflows render Claude review blocks
The `/opsx:*` lifecycle workflows that invoke Claude artifact review SHALL instruct Codex to render the mandatory `## Claude Review` block after each reviewer helper call and before checkpointing or continuing to dependent lifecycle work.

#### Scenario: Continue renders the review block
- **GIVEN** `/opsx:continue` creates a reviewable artifact
- **AND** the Claude artifact review helper is invoked or skipped for that stage
- **WHEN** Codex reaches the post-artifact checkpoint boundary
- **THEN** Codex renders `## Claude Review` with `Budget / cost`, `User-facing questions`, and `Short summary` sections
- **AND** Codex does not treat raw helper JSON output as sufficient disclosure

#### Scenario: Fast-forward and propose render review blocks per stage
- **GIVEN** `/opsx:ff` or `/opsx:propose` generates multiple artifacts in sequence
- **AND** Claude review is invoked or skipped for one or more stages
- **WHEN** each stage reaches its review boundary
- **THEN** Codex renders a separate `## Claude Review` block for that stage
- **AND** Codex stops the batch if the block contains a user-required question or unhandled blocker

#### Scenario: Apply renders review blocks and final aggregate cost
- **GIVEN** `/opsx:apply` invokes Claude artifact review during implementation or verification steps
- **WHEN** each reviewer call completes
- **THEN** Codex renders `## Claude Review` for the call before continuing
- **AND** the final apply summary still includes the aggregate Claude reviewer cost when available according to the existing apply cost-summary behavior

### Requirement: README groups Claude review controls with review documentation
The README command and workflow documentation SHALL place Claude review session controls near the optional Claude artifact review explanation so users can discover enablement, status, reset, model, effort, and budget controls while reading the review workflow documentation.

#### Scenario: English README places controls near Claude review
- **GIVEN** a user reads `README.md`
- **WHEN** they reach the optional Claude artifact review section
- **THEN** the Claude review session controls section appears before unrelated project context, installation, version, or license sections
- **AND** the controls still list or cross-reference `/opsx:claude-review-status`, `/opsx:claude-review-on`, `/opsx:claude-review-off`, `/opsx:claude-review-reset`, and `/opsx:claude-review-set`

