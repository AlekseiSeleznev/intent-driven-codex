## ADDED Requirements

### Requirement: Claude reviewer cost is surfaced after each invocation
When Claude Code returns cost metadata for an artifact review, the system MUST expose the review call's `total_cost_usd` to Codex workflow output and persisted report consumers without treating the presence of that cost as a blocking condition.

#### Scenario: Successful review reports call cost
- **GIVEN** Claude artifact review is enabled for a lifecycle stage
- **AND** Claude Code returns a structured review result with `total_cost_usd`
- **WHEN** Codex processes the reviewer result
- **THEN** Codex records the call cost in the structured review report
- **AND** Codex reports that call cost to the user before the next dependent artifact consumes the reviewed artifact
- **AND** Codex continues according to the review verdict and blocking policy, not because a normal cost value was present

#### Scenario: Missing cost remains non-blocking
- **GIVEN** Claude artifact review is enabled for a lifecycle stage
- **AND** Claude Code returns a structured review result without `total_cost_usd`
- **WHEN** Codex processes the reviewer result
- **THEN** Codex reports that the call cost was unavailable when cost output is expected
- **AND** Codex does not block lifecycle progress solely because cost metadata is missing

#### Scenario: Budget exhaustion remains a blocker
- **GIVEN** Claude artifact review is enabled for a lifecycle stage
- **AND** Claude Code reports that the configured maximum budget was exhausted
- **WHEN** Codex processes the reviewer result
- **THEN** Codex reports the budget-exhaustion blocker and any available cost metadata
- **AND** dependent lifecycle progress stops according to the budget-exhaustion fail-safe
