## ADDED Requirements

### Requirement: Installation documentation remains executable
The template MUST keep Greenfield, Brownfield, and manual installation instructions aligned with the installer behavior and the files required by `scripts/check-overlay`, so a user following the documented path can reach a green overlay check without undocumented file copies.

#### Scenario: Greenfield docs use force overlay replacement
- **GIVEN** a Greenfield repository has just run `openspec init . --tools codex`
- **WHEN** the user follows the template's Greenfield installation instructions
- **THEN** the documented installer command includes `--force-overlay`
- **AND** placeholder OpenSpec/Codex prompts and skills are replaced by the intent-driven overlay assets

#### Scenario: Manual install lists all required overlay assets
- **GIVEN** a user follows the manual installation procedure instead of `scripts/install-overlay`
- **WHEN** the user copies the documented files into the target repository
- **THEN** the documented list includes every script, config, prompt, skill, doc, and ignore rule required for `scripts/check-overlay` to pass
- **AND** the manual path does not omit `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, `scripts/openspec-session-state`, `scripts/openspec-git-checkpoint`, or `.codex/session/` ignore guidance

#### Scenario: Greenfield docs do not overwrite context after install
- **GIVEN** the installer has already created or preserved `CONSTITUTION.md`, `ARCHITECTURE.md`, and local secret examples
- **WHEN** the user reads the post-install documentation steps
- **THEN** the instructions tell the user to review and edit those files rather than blindly copy over them
- **AND** existing project-owned context is not overwritten by a bare `cp` command

### Requirement: Release documentation identifies the shipped template state
The template SHALL keep version, changelog, README badges, current-state narrative, active change list, archived change list, and in-force ADR summaries consistent with the repository state being shipped.

#### Scenario: Version and changelog describe recent features
- **GIVEN** a release includes new Claude review, session-control, git-checkpoint, or documentation-fix behavior
- **WHEN** a user reads `VERSION`, `CHANGELOG.md`, and README state sections
- **THEN** the version and changelog describe the shipped feature set consistently
- **AND** README badges and state text do not advertise an older feature baseline

#### Scenario: README state matches OpenSpec state
- **GIVEN** there are no active OpenSpec changes
- **WHEN** a user reads the README current-state table
- **THEN** the table says there are no active changes
- **AND** archived changes listed or referenced by the README match `openspec/changes/archive/`
