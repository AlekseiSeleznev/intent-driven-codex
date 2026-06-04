## ADDED Requirements

### Requirement: Claude artifact review remains legacy-disabled
The system MUST keep legacy Claude review assets in the codebase for compatibility while ensuring intent-driven lifecycle commands do not ask for, enable, or invoke Claude review by default or through current provider-neutral review controls.

#### Scenario: Lifecycle ignores legacy Claude config
- **GIVEN** `.codex/openspec-claude-review.json` exists
- **AND** `scripts/openspec-claude-review` exists
- **WHEN** Codex creates, fast-forwards, applies, or verifies an intent-driven change
- **THEN** Codex does not invoke `scripts/openspec-claude-review`
- **AND** Codex uses provider-neutral artifact review settings if review is enabled

#### Scenario: Legacy files are retained
- **GIVEN** the repository contains legacy Claude review helper files or prompts
- **WHEN** this change is implemented
- **THEN** those legacy files may remain present
- **AND** documentation identifies them as disabled legacy assets rather than the active reviewer path

#### Scenario: Old Claude session decisions do not activate review
- **GIVEN** ignored session state contains a legacy `claudeReview` decision
- **WHEN** Codex resolves current artifact review settings
- **THEN** that legacy decision does not enable current artifact review
- **AND** current review enablement comes from `artifactReview` state and `.codex/openspec-artifact-review.json`
