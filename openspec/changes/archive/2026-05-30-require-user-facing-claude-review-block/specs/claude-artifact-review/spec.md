## ADDED Requirements

### Requirement: Claude review results are summarized for users after each invocation
The system MUST require Codex to render a user-facing `## Claude Review` block after every Claude artifact review helper invocation, including successful reviews, skipped reviews, unavailable reviews, parse/config failures, and budget-exhaustion blockers. The helper output itself SHALL NOT be considered sufficient user-facing disclosure.

#### Scenario: Review block reports cost and configured budget cap
- **GIVEN** a Claude artifact review helper invocation has completed for a lifecycle stage
- **WHEN** Codex reports the result to the user
- **THEN** Codex includes a `## Claude Review` block
- **AND** the block includes `Budget / cost`
- **AND** the block reports `cost.total_cost_usd` when that numeric value is present
- **AND** the block reports that cost is unavailable when no numeric cost metadata is present
- **AND** the block reports the configured `maxBudgetUsd` budget cap, including `null` or `none` when no cap is configured

#### Scenario: Skipped or unavailable review still reports budget status
- **GIVEN** a lifecycle stage review is skipped by configuration or unavailable policy
- **WHEN** Codex reports the stage outcome to the user
- **THEN** Codex still includes a `## Claude Review` block
- **AND** the block states that review did not run or was unavailable
- **AND** the block reports cost as unavailable
- **AND** the block reports the effective budget cap value or `none`

#### Scenario: User-facing questions are separated from internal findings
- **GIVEN** a Claude review result includes reviewer questions or clarifications
- **WHEN** Codex evaluates those questions using repository context and artifacts
- **THEN** Codex includes only questions that require a user decision in the `User-facing questions` section
- **AND** Codex explicitly states `Вопросов, требующих участия пользователя: нет.` when no user decision is required
- **AND** Codex may resolve questions answerable from context itself and summarize the resolution without stopping

#### Scenario: Summary includes finding disposition
- **GIVEN** a Claude review result includes a verdict and findings
- **WHEN** Codex reports the result to the user
- **THEN** the `Short summary` section states the verdict
- **AND** it briefly summarizes what Claude said without dumping every non-user-actionable finding
- **AND** it states whether findings were fixed and review rerun, deferred to a concrete artifact/file/section, treated as non-actionable with a reason, or surfaced as a blocker/question

### Requirement: Claude findings are handled before checkpoint or dependent progress
The system MUST ensure every actionable Claude review finding is handled before Codex creates a checkpoint commit or allows a later lifecycle artifact or apply step to consume the reviewed artifact.

#### Scenario: Actionable findings block checkpoint until disposition exists
- **GIVEN** Claude artifact review reports a must-fix, actionable should-fix, warning, or question
- **WHEN** Codex reaches the checkpoint or dependent-artifact boundary
- **THEN** Codex either fixes the finding, defers it to a concrete next artifact/file/section, marks it non-actionable with a reason, or asks the user and stops
- **AND** Codex does not checkpoint while any Claude finding remains unhandled

#### Scenario: User decision questions stop progress
- **GIVEN** Claude returns a question that cannot be answered from repository context or existing artifacts
- **WHEN** Codex prepares to continue workflow execution
- **THEN** Codex includes the question in the `User-facing questions` section
- **AND** Codex stops before checkpointing or creating dependent artifacts until the user answers or explicitly overrides the blocker

#### Scenario: Pass or warn verdict does not hide questions
- **GIVEN** Claude returns a `pass` or `warn` verdict with questions
- **WHEN** Codex evaluates the result
- **THEN** Codex decides whether each question requires the user or can be answered from context
- **AND** Codex surfaces any user-required question even though the verdict is non-failing
- **AND** Codex records the disposition of context-resolved questions in the `Short summary`
