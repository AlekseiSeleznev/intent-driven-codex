## ADDED Requirements

### Requirement: Installed overlays include Matt grill and TDD assets
The installer and global overlay update process MUST install the vendored `grill-with-docs` and `tdd` skills, related reference files, schema templates, prompts, and docs needed for the hardened lifecycle.

#### Scenario: Greenfield install includes hardened skills
- **GIVEN** a target project receives the overlay with `scripts/install-overlay --force-overlay`
- **WHEN** installation completes
- **THEN** `.codex/skills/grill-with-docs` contains Matt grill guidance and context/ADR format references
- **AND** `.codex/skills/tdd` contains Matt TDD guidance and supporting references

#### Scenario: Overlay check validates hardened lifecycle
- **GIVEN** the overlay checker runs in a project with the intent-driven schema
- **WHEN** it creates a smoke change
- **THEN** it expects the artifact order `proposal`, `specs`, `grill`, `design`, `design-review`, `adr`, `test-plan`, `tasks`
- **AND** it verifies apply context includes the hardened gate files
