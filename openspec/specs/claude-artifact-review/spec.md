# claude-artifact-review Specification

## Purpose
Defines optional Claude Code artifact review for generated OpenSpec artifacts, including non-secret stage configuration, model and effort handling, structured review reports, safe review inputs, and dependent-artifact gating policy.
## Requirements
### Requirement: Claude artifact review is configurable by stage
The system MUST provide a non-secret configuration model that enables or disables Claude Code artifact review globally and per OpenSpec lifecycle stage, with stage-specific overrides for model, effort, fallback model, maximum budget, prompt profile, report persistence, and blocking policy.

#### Scenario: Stage inherits global defaults
- **GIVEN** Claude artifact review is globally enabled
- **AND** the `specs` stage does not define a model override
- **WHEN** Codex resolves review configuration for the `specs` stage
- **THEN** Codex uses the configured global model default
- **AND** Codex uses any stage-specific overrides that are present

#### Scenario: Stage disables review
- **GIVEN** Claude artifact review is globally enabled
- **AND** the `grill` stage has `enabled: false`
- **WHEN** Codex completes `grill.md`
- **THEN** Codex does not invoke `claude -p` for that stage
- **AND** Codex records or reports that the stage review was skipped by configuration when review status is requested

#### Scenario: Stage chooses pinned model and effort
- **GIVEN** the `design-review` stage is configured with `model: claude-opus-4-8` and `effort: high`
- **WHEN** Codex invokes the Claude artifact reviewer for `design-review.md`
- **THEN** the invocation includes `--model claude-opus-4-8`
- **AND** the invocation includes `--effort high`

### Requirement: Claude model resolution supports aliases and pinned IDs
The system MUST support both Claude Code model aliases and full Claude model IDs, and SHALL recommend full model IDs when review reproducibility is more important than automatically tracking the provider's latest alias target.

#### Scenario: Full model ID is passed through
- **GIVEN** a review stage is configured with `model: claude-sonnet-4-6`
- **WHEN** Codex builds the `claude -p` command
- **THEN** Codex passes `--model claude-sonnet-4-6` without rewriting it to an alias

#### Scenario: Alias is allowed explicitly
- **GIVEN** a review stage is configured with `model: sonnet`
- **WHEN** Codex builds the `claude -p` command
- **THEN** Codex passes `--model sonnet`
- **AND** the review report identifies that the configured model is an alias whose resolved target may vary by provider and time

#### Scenario: Unsupported model configuration fails safely
- **GIVEN** a review stage is configured with an empty or invalid model value
- **WHEN** Codex resolves reviewer configuration
- **THEN** Codex does not run `claude -p`
- **AND** Codex reports the configuration error without modifying OpenSpec artifacts

### Requirement: Claude effort is model-aware
The system MUST validate or safely handle configured effort levels according to the configured model family, including avoiding unsupported effort flags for models that do not support them.

#### Scenario: Opus supports xhigh effort
- **GIVEN** a review stage is configured with `model: claude-opus-4-8` and `effort: xhigh`
- **WHEN** Codex validates reviewer configuration
- **THEN** Codex accepts the configuration
- **AND** Codex passes `--effort xhigh` to `claude -p`

#### Scenario: Sonnet xhigh is rejected or downgraded by policy
- **GIVEN** a review stage is configured with `model: claude-sonnet-4-6` and `effort: xhigh`
- **WHEN** Codex validates reviewer configuration in strict mode
- **THEN** Codex reports that `xhigh` is not a supported Sonnet 4.6 effort level
- **AND** Codex does not run the review until the stage configuration is corrected

#### Scenario: Haiku omits effort
- **GIVEN** a review stage is configured with `model: claude-haiku-4-5-20251001`
- **AND** the resolved model capabilities do not support Claude Code effort
- **WHEN** Codex builds the `claude -p` command
- **THEN** Codex omits the `--effort` flag
- **AND** the review report records that no effort setting was applied

### Requirement: Claude artifact review uses non-interactive structured output
The system MUST invoke Claude Code in non-interactive print mode and request structured output so Codex can process reviewer findings deterministically.

#### Scenario: Review command uses print mode JSON
- **GIVEN** a review stage is enabled
- **WHEN** Codex runs the reviewer
- **THEN** the command uses `claude -p`
- **AND** the command requests JSON or JSON-schema-validated output
- **AND** the command applies the configured maximum budget when present

#### Scenario: Structured output is valid
- **GIVEN** Claude returns a successful structured review result
- **WHEN** Codex parses the reviewer output
- **THEN** Codex reads a verdict such as `pass`, `warn`, `fail`, or `blocked`
- **AND** Codex records must-fix findings, should-fix findings, questions, reviewed artifact paths, artifact hashes, model, effort, cost metadata when available, and the review timestamp

#### Scenario: Structured output is invalid
- **GIVEN** Claude exits successfully but returns invalid or schema-incompatible output
- **WHEN** Codex parses the reviewer output
- **THEN** Codex treats the review as failed or blocked according to configured policy
- **AND** Codex reports the parse error without guessing reviewer intent from free-form text

### Requirement: Review inputs preserve project context and secret boundaries
The system MUST build review prompts or file bundles from the generated artifact and relevant non-secret project context while excluding local secret values and unrelated large files.

#### Scenario: Proposal review receives focused context
- **GIVEN** `proposal.md` has just been generated
- **WHEN** Codex builds the proposal review input
- **THEN** the input includes `proposal.md` and relevant non-secret project context such as constitution, glossary, architecture snapshot, and in-force ADR summaries
- **AND** the input excludes `.secrets.local.env` and other local secret files

#### Scenario: Design review receives prior artifacts
- **GIVEN** `design-review.md` has just been generated
- **WHEN** Codex builds the design-review review input
- **THEN** the input includes proposal, specs, grill, design, and design-review artifacts
- **AND** the input includes relevant architecture and ADR context needed to review design consistency

#### Scenario: Oversized review input is bounded
- **GIVEN** the candidate review input exceeds the configured size limit
- **WHEN** Codex prepares the review
- **THEN** Codex applies the configured truncation, manifest, or file-access strategy
- **AND** the review report identifies any omitted context that may affect confidence

### Requirement: Reviewer findings gate dependent artifacts according to policy
The system MUST apply the configured blocking policy after a Claude review and before a dependent lifecycle artifact consumes the reviewed artifact.

#### Scenario: Fail verdict blocks next artifact
- **GIVEN** a stage is configured with `blockOn: fail`
- **AND** Claude returns a `fail` verdict with must-fix findings
- **WHEN** Codex prepares to create the next dependent artifact
- **THEN** Codex stops before creating that artifact
- **AND** Codex reports the must-fix findings and the reviewed artifact path

#### Scenario: Warn verdict can continue
- **GIVEN** a stage is configured to allow warnings
- **AND** Claude returns a `warn` verdict with should-fix findings only
- **WHEN** Codex prepares to create the next dependent artifact
- **THEN** Codex may continue after recording the warning
- **AND** Codex includes the warning in the review report or lifecycle summary

#### Scenario: Reviewer unavailable follows configured fallback
- **GIVEN** Claude artifact review is enabled for a stage
- **AND** the `claude` executable is unavailable or unauthenticated
- **WHEN** Codex tries to run the reviewer
- **THEN** Codex follows the stage's configured unavailable policy
- **AND** Codex reports whether the stage is blocked, skipped, or requires manual override

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
