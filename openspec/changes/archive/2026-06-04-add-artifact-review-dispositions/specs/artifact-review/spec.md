## ADDED Requirements

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
