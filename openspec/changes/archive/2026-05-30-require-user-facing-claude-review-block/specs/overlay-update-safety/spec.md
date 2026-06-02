## ADDED Requirements

### Requirement: Overlay checks guard Claude review block guidance
Overlay verification MUST include deterministic checks that relevant project-local prompts, skills, documentation, and helper report paths preserve the mandatory user-facing Claude review block behavior.

#### Scenario: Prompt and skill checks require review block markers
- **GIVEN** a repository contains the intent-driven Codex overlay
- **WHEN** `scripts/check-overlay` runs
- **THEN** it checks every prompt or skill that invokes `scripts/openspec-claude-review` for `## Claude Review` guidance
- **AND** it checks for `Budget / cost`, `maxBudgetUsd`, the no-user-question phrase, and the no-unhandled-finding checkpoint rule

#### Scenario: Documentation checks cover review block expectations
- **GIVEN** lifecycle or update-safety documentation describes Claude artifact review
- **WHEN** `scripts/check-overlay` runs documentation drift checks
- **THEN** it verifies the documentation states the required `## Claude Review` block and budget/question behavior

#### Scenario: Helper reports expose configured budget cap
- **GIVEN** `scripts/openspec-claude-review` produces a structured report for a skipped, parsed, unavailable, or real review result
- **WHEN** `scripts/check-overlay` validates reviewer report shape
- **THEN** the report includes `budgetLimitUsd`
- **AND** Codex can use that field to display the configured `maxBudgetUsd` or `none` in the user-facing block
