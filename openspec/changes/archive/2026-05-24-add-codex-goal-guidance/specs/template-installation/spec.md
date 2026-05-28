## MODIFIED Requirements

### Requirement: Installed documentation explains workflow behavior
The template MUST include documentation that explains what is invoked at each lifecycle stage, optional skill conditions, generated Codex Goal prompts for long apply and bulk-apply runs, Greenfield flow, Brownfield flow, git checkpoints, ADR lifecycle, and update safety.

#### Scenario: User reads installed functionality docs
- **GIVEN** the template has been installed
- **WHEN** the user opens the workflow documentation
- **THEN** the documentation describes each `/opsx:*` command and its OpenSpec/skill behavior
- **AND** it identifies which behaviors are mandatory and which are optional
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
