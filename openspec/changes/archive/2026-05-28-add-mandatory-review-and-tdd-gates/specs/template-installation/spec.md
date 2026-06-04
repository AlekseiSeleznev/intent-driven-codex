## MODIFIED Requirements

### Requirement: Greenfield installation is repeatable
The template SHALL support installation into a new repository and leave it ready to create intent-driven OpenSpec changes with mandatory review and test-plan gates immediately.

#### Scenario: Greenfield install enables intent-driven schema
- **GIVEN** a new repository has OpenSpec initialized for Codex
- **WHEN** the template assets are installed
- **THEN** `openspec/config.yaml` selects `schema: intent-driven`
- **AND** `openspec schema validate intent-driven` succeeds

#### Scenario: Greenfield smoke check reaches apply-ready state
- **GIVEN** the template is installed in a new repository
- **WHEN** Codex runs the full smoke check
- **THEN** a smoke change can create proposal, specs, design, review, ADR review, test-plan, and tasks artifacts
- **AND** `openspec instructions apply --change <smoke> --json` returns ready context including review and test-plan files

### Requirement: Installed documentation explains workflow behavior
The template MUST include documentation that explains what is invoked at each lifecycle stage, mandatory `grill-with-docs` review, mandatory test-first verification planning, generated Codex Goal prompts for long apply and bulk-apply runs, Greenfield flow, Brownfield flow, git checkpoints, ADR lifecycle, and update safety.

#### Scenario: User reads installed functionality docs
- **GIVEN** the template has been installed
- **WHEN** the user opens the workflow documentation
- **THEN** the documentation describes each `/opsx:*` command and its OpenSpec/skill behavior
- **AND** it identifies which behaviors are mandatory and which are optional
- **AND** it explains that development and enhancement changes pass through `review.md` and `test-plan.md` before implementation
- **AND** it explains that long `/opsx:apply` and eligible `/opsx:bulk-apply` runs can generate a copy-paste `/goal` prompt before implementation begins

#### Scenario: User learns how to run a generated apply goal
- **GIVEN** a project has the template documentation installed
- **WHEN** the user reads the apply or bulk-apply guidance
- **THEN** the documentation shows that the generated prompt starts with `/goal`
- **AND** the prompt body includes the concrete `/opsx:apply <change>` or `/opsx:bulk-apply <changes...>` workflow, completion criteria, and stop-without-completion conditions
- **AND** the documentation states that the user should copy and send the generated prompt as the next message when they want Codex Goal to manage the run

#### Scenario: User learns when goal guidance is skipped
- **GIVEN** a project has the template documentation installed
- **WHEN** the user reads the apply or bulk-apply guidance
- **THEN** the documentation explains that goal guidance is skipped when the run is already inside an active goal, the work is small enough for normal apply, or the user explicitly requests to continue without a goal
- **AND** the documentation states that OpenSpec artifacts remain the source of truth even when Codex Goal manages execution
