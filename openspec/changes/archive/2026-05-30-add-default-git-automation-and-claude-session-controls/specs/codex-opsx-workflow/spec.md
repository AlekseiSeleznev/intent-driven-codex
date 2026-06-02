## MODIFIED Requirements

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

## ADDED Requirements

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
