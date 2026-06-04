# artifact-review Specification

## Purpose
TBD - created by archiving change replace-claude-review-with-openai-compatible-review. Update Purpose after archive.
## Requirements
### Requirement: Artifact review uses an OpenAI-compatible provider
The system MUST provide an optional artifact reviewer that calls an OpenAI-compatible chat completions API using non-secret configuration for provider URL, API key environment variable name, model, request options, and stage policy.

#### Scenario: Provider configuration is non-secret
- **GIVEN** artifact review is configured for a project
- **WHEN** Codex reads the tracked review configuration
- **THEN** the configuration contains the API base URL and API key environment variable name
- **AND** it does not contain the API key value or other secret values

#### Scenario: Polza base URL is supported
- **GIVEN** the provider base URL is `https://polza.ai/api/v1`
- **AND** `apiKeyEnv` is `POLZA_AI_API_KEY`
- **WHEN** the reviewer builds a chat completions request
- **THEN** it posts to `https://polza.ai/api/v1/chat/completions`
- **AND** it authenticates with `Authorization: Bearer <POLZA_AI_API_KEY>` using the local environment value without printing it

#### Scenario: DeepSeek Pro can be configured
- **GIVEN** the model is configured as `deepseek/deepseek-v4-pro`
- **WHEN** artifact review runs
- **THEN** the request uses that model identifier
- **AND** the persisted report records the configured model without recording credentials

### Requirement: Artifact review supports stage-specific policy
The system MUST enable or disable artifact review globally and per OpenSpec lifecycle stage, with stage-specific overrides for model, effort, reasoning, temperature, token limits, maximum budget, prompt profile, report persistence, unavailable policy, and blocking policy.

#### Scenario: Stage inherits defaults
- **GIVEN** artifact review is globally enabled
- **AND** the `specs` stage does not define a model override
- **WHEN** Codex resolves review configuration for `specs`
- **THEN** Codex uses the configured default model and request options
- **AND** applies any `specs` overrides that are present

#### Scenario: Stage disables review
- **GIVEN** artifact review is globally enabled
- **AND** the `grill` stage has `enabled: false`
- **WHEN** Codex completes `grill.md`
- **THEN** Codex does not call the OpenAI-compatible provider for that stage
- **AND** status output reports that the stage review is skipped by configuration

#### Scenario: Effort maps to reasoning options
- **GIVEN** a stage configures `effort: high`
- **WHEN** the reviewer builds the request body
- **THEN** it includes configured reasoning fields when the stage or defaults enable reasoning
- **AND** it records the configured and effective effort in the report

### Requirement: Artifact review produces structured findings
The system MUST request and parse a deterministic JSON review with `verdict`, `summary`, `mustFix`, `shouldFix`, and `questions` while supporting providers that do not enforce JSON schema natively.

#### Scenario: Structured outputs are requested when enabled
- **GIVEN** a provider or model supports `response_format`
- **WHEN** the reviewer builds a request with structured output mode enabled
- **THEN** it includes a JSON schema response format for the review result

#### Scenario: Prompt-only JSON remains supported
- **GIVEN** a provider or model does not support `response_format`
- **WHEN** the reviewer builds a request
- **THEN** the system prompt requires JSON-only output matching the review schema
- **AND** the parser validates the returned content before accepting the review

#### Scenario: Invalid structured output blocks safely
- **GIVEN** the provider returns text that is not valid review JSON
- **WHEN** Codex parses the response
- **THEN** the review result is `blocked` or fails according to policy
- **AND** Codex reports the parse error without guessing reviewer intent

### Requirement: Review inputs preserve context and secret boundaries
The system MUST build review prompts or bundles from the generated artifact and relevant non-secret project context while excluding local secret values and unrelated large files.

#### Scenario: Secret files are omitted
- **GIVEN** `.secrets.local.env` exists in the project
- **WHEN** the reviewer builds the review input
- **THEN** `.secrets.local.env` is not read into the prompt bundle
- **AND** the report may list the file as omitted without revealing its contents

#### Scenario: Later stages include prior artifacts
- **GIVEN** `design-review.md` has just been generated
- **WHEN** the reviewer builds the design-review input
- **THEN** the input includes proposal, specs, grill, design, and design-review artifacts
- **AND** it includes relevant non-secret architecture and ADR context

### Requirement: Review findings gate lifecycle progress
The system MUST apply the configured blocking policy after artifact review and before dependent lifecycle artifacts or apply steps consume the reviewed artifact.

#### Scenario: Fail verdict blocks progress
- **GIVEN** a stage is configured with `blockOn: ["fail", "blocked"]`
- **AND** the reviewer returns `fail`
- **WHEN** Codex prepares the next dependent artifact
- **THEN** Codex stops before creating that artifact
- **AND** reports the must-fix findings and reviewed artifact path

#### Scenario: Warn verdict can continue with disposition
- **GIVEN** a stage allows `warn` verdicts
- **AND** the reviewer returns actionable `shouldFix` findings
- **WHEN** Codex reaches the checkpoint boundary
- **THEN** Codex fixes, defers, or marks each finding non-actionable with reason before checkpointing or continuing

### Requirement: Review reports include cost and budget information
The system MUST surface provider cost metadata when available and MUST preserve the configured budget cap in each review report and user-facing summary.

#### Scenario: Polza usage cost is recorded
- **GIVEN** Polza.ai returns `usage.cost` or `usage.cost_rub`
- **WHEN** the reviewer parses the response
- **THEN** the persisted report records the numeric cost and currency when available
- **AND** the user-facing summary reports the cost before dependent progress

#### Scenario: Missing cost is non-blocking
- **GIVEN** the provider returns no numeric cost metadata
- **WHEN** Codex processes a successful review
- **THEN** Codex reports cost as unavailable
- **AND** progress is determined by verdict and blocking policy rather than missing cost alone

### Requirement: Review session controls are provider-neutral
The system MUST expose provider-neutral review status, enable, disable, reset, and set controls for the current ignored session overlay without requiring users to edit tracked project defaults.

#### Scenario: Session overlay enables artifact review
- **GIVEN** tracked artifact review defaults are disabled
- **WHEN** the user enables review for the current session
- **THEN** Codex records an ignored `artifactReview` session decision
- **AND** leaves tracked `.codex/openspec-artifact-review.json` unchanged unless explicitly requested

#### Scenario: Stage settings are updated without secrets
- **GIVEN** the user sets a stage model, effort, base URL, or budget
- **WHEN** the session-state helper records the setting
- **THEN** it validates secret-looking keys are not present
- **AND** it stores only non-secret values such as model IDs, URLs, and environment variable names

### Requirement: Artifact review records formal finding dispositions
The system MUST support a machine-readable disposition record for every reviewer finding that could affect lifecycle progress, without trusting the reviewer verdict as the final decision.

#### Scenario: Findings require explicit disposition before progress
- **GIVEN** an OpenAI-compatible artifact review report contains `mustFix`, actionable `shouldFix`, warnings, or questions
- **WHEN** Codex prepares to checkpoint, create a dependent artifact, verify, or archive the change
- **THEN** every such finding has a disposition status of `fixed`, `deferred`, `non_actionable`, or `user_blocked`
- **AND** each disposition records a short reason
- **AND** deferred dispositions identify a concrete artifact, file, section, or task target

#### Scenario: User questions are separated from context-resolved questions
- **GIVEN** a reviewer question can be answered from project artifacts, specs, ADRs, constitution, code, or docs
- **WHEN** Codex handles the review report
- **THEN** Codex records a non-user-blocking disposition with the evidence-based reason
- **AND** the compact user-facing status does not ask the user that question

#### Scenario: Open findings block lifecycle progress
- **GIVEN** a persisted review report has findings that affect progress
- **AND** no corresponding disposition record closes them
- **WHEN** an overlay check or lifecycle gate validates review state
- **THEN** the check reports unresolved review findings
- **AND** Codex does not silently checkpoint or continue dependent work

### Requirement: Artifact review status is compact and localized
The system MUST render user-facing artifact review status as a compact localized block in the current chat language, while keeping machine-readable report keys stable.

#### Scenario: Russian status uses localized labels and separators
- **GIVEN** the current chat language is Russian
- **AND** a review for `test-plan` passes with no open findings
- **WHEN** Codex reports the review result to the user
- **THEN** the output is wrapped between `---` separator lines
- **AND** it uses localized labels such as `Ревью артефакта`, `Стоимость`, and `Замечания`
- **AND** it avoids raw English user-facing labels such as `Artifact Review`, `Findings`, `mustFix`, or `shouldFix`

#### Scenario: Compact status summarizes dispositions
- **GIVEN** a review report has finding dispositions
- **WHEN** Codex renders the user-facing status
- **THEN** it summarizes counts for critical findings, suggested findings, warnings, and user questions
- **AND** it states whether everything is closed, there are open blockers, or a user decision is required
- **AND** it does not print every finding unless user participation is required

#### Scenario: Displayed RUB costs round upward to kopecks
- **GIVEN** review cost metadata includes RUB values with more than two decimal places
- **WHEN** Codex renders the user-facing status
- **THEN** each displayed RUB amount is rounded upward to two decimal places
- **AND** the decimal separator follows the chat language convention when known

### Requirement: Artifact review helper can summarize status inputs
The artifact review helper SHALL expose enough persisted summary data for Codex prompts and checks to render compact review status without re-contacting the provider.

#### Scenario: Cost summary includes display-ready rounded RUB values
- **GIVEN** review invocation ledger entries include numeric RUB costs
- **WHEN** the helper computes a cost summary
- **THEN** it includes raw numeric totals for machine use
- **AND** it includes upward-rounded RUB display values for user-facing summaries

#### Scenario: Disposition summary can be validated without provider access
- **GIVEN** a change has persisted review reports and disposition records
- **WHEN** the overlay checker validates review disposition fixtures
- **THEN** it verifies closed/open counts from local files only
- **AND** it does not read local secret files or contact the provider
