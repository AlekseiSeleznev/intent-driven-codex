## ADDED Requirements

### Requirement: Opsx command documentation covers shipped commands
The README command documentation SHALL list or cross-reference all shipped `/opsx:*` prompts that users need for the current workflow, including Claude review session controls.

#### Scenario: Claude review commands appear in command docs
- **GIVEN** the template ships `/opsx:claude-review-status`, `/opsx:claude-review-on`, `/opsx:claude-review-off`, `/opsx:claude-review-reset`, and `/opsx:claude-review-set`
- **WHEN** a user reads the main command table or its immediate cross-reference
- **THEN** those commands are discoverable from the command documentation
- **AND** the documentation says they change session overlays rather than project-global defaults unless explicitly requested

### Requirement: Support skill names are documented consistently
Workflow documentation MUST use installed skill identifiers consistently when naming support skills that Codex can invoke.

#### Scenario: Diagram skill uses canonical name
- **GIVEN** the installed support skill is named `c4-diagrams`
- **WHEN** README or lifecycle documentation refers to that support skill
- **THEN** it uses `c4-diagrams` or clearly labels any human-friendly name as referring to `c4-diagrams`
