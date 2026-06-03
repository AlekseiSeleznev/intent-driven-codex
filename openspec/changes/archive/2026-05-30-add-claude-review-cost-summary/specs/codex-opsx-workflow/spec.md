## ADDED Requirements

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
