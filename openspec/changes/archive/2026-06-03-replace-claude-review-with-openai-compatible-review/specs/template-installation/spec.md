## ADDED Requirements

### Requirement: Opsx exposes provider-neutral artifact review session commands
The template MUST provide Codex-native `/opsx:*` commands for showing, enabling, disabling, and updating provider-neutral artifact review session settings without requiring users to edit the project-global review configuration by hand, and legacy Claude-specific commands MUST NOT be the active workflow controls.

#### Scenario: Status command shows effective review settings
- **GIVEN** a project has `.codex/openspec-artifact-review.json`
- **WHEN** the user invokes `/opsx:review-status`
- **THEN** Codex reports whether artifact review is enabled for the current session
- **AND** Codex shows effective stage settings after applying session overlays while omitting any secret values

#### Scenario: Enable command turns on session review
- **GIVEN** artifact review is globally disabled in the tracked project config
- **WHEN** the user invokes `/opsx:review-on`
- **THEN** Codex records a session overlay that enables artifact review for the current session
- **AND** Codex leaves `.codex/openspec-artifact-review.json` unchanged unless the user explicitly asks to change global defaults

#### Scenario: Disable command turns off session review
- **GIVEN** artifact review is enabled by global config or session overlay
- **WHEN** the user invokes `/opsx:review-off`
- **THEN** Codex records a session overlay that disables artifact review for the current session
- **AND** later `/opsx:*` lifecycle commands skip provider calls because of that session decision

#### Scenario: Set command updates provider and stage options
- **GIVEN** a user wants different review depth or provider settings
- **WHEN** the user invokes `/opsx:review-set --stage design-review --model deepseek/deepseek-v4-pro --effort high`
- **THEN** Codex validates the non-secret settings
- **AND** Codex records the setting in the session overlay if validation succeeds

### Requirement: Opsx apply reports aggregate artifact reviewer cost
The `/opsx:apply` workflow SHALL summarize total provider-neutral artifact reviewer cost for the apply run or change when review calls have produced cost metadata, while preserving existing apply, TDD, checkpoint, and review blocking behavior.

#### Scenario: Apply final summary includes total review cost
- **GIVEN** `/opsx:apply` invokes OpenAI-compatible artifact review one or more times during the apply run
- **AND** the reviewer results include numeric cost values
- **WHEN** Codex completes the apply workflow summary
- **THEN** Codex reports the aggregate artifact review cost for the apply run
- **AND** Codex identifies that the total covers external reviewer calls, not unrelated Codex or OpenSpec CLI execution

#### Scenario: Apply can summarize persisted review report costs
- **GIVEN** a change has persisted artifact review reports under `openspec/changes/<change>/reviews/`
- **AND** those reports include cost metadata
- **WHEN** Codex needs a final review cost summary for `/opsx:apply`
- **THEN** Codex can compute the aggregate from the persisted reports without invoking a provider again
- **AND** the summary excludes reports that do not contain numeric cost values

#### Scenario: Cost summary does not replace review gating
- **GIVEN** a review report has a blocking verdict or a budget-exhaustion result
- **WHEN** `/opsx:apply` prepares its summary
- **THEN** Codex still applies the configured blocker behavior
- **AND** the cost summary is informational and does not override the blocker
