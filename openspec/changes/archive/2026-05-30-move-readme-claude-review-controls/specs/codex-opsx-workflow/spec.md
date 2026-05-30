## ADDED Requirements

### Requirement: README groups Claude review controls with review documentation
The README command and workflow documentation SHALL place Claude review session controls near the optional Claude artifact review explanation so users can discover enablement, status, reset, model, effort, and budget controls while reading the review workflow documentation.

#### Scenario: English README places controls near Claude review
- **GIVEN** a user reads `README.md`
- **WHEN** they reach the optional Claude artifact review section
- **THEN** the Claude review session controls section appears before unrelated project context, installation, version, or license sections
- **AND** the controls still list or cross-reference `/opsx:claude-review-status`, `/opsx:claude-review-on`, `/opsx:claude-review-off`, `/opsx:claude-review-reset`, and `/opsx:claude-review-set`

#### Scenario: Russian README mirrors the placement
- **GIVEN** a user reads `README.ru.md`
- **WHEN** they reach the optional Claude artifact review section
- **THEN** the Russian Claude session controls section appears before unrelated project context, installation, version, or license sections
- **AND** the controls remain natural Russian text and preserve the same command coverage as the English README
