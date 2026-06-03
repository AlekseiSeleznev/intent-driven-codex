## ADDED Requirements

### Requirement: Documentation drift checks cover repository state claims
Overlay verification SHALL include automated or documented checks that compare public documentation claims against repository state for active changes, archived changes, in-force ADRs, installer-owned assets, and common command paths.

#### Scenario: Active and archived change claims are checkable
- **GIVEN** README documentation states active and archived OpenSpec change state
- **WHEN** Codex runs documentation drift checks
- **THEN** the checks compare active changes with `openspec list --json`
- **AND** archived change claims are compared with `openspec/changes/archive/`

#### Scenario: Installer manual list is checkable
- **GIVEN** installation documentation lists manual copy assets
- **WHEN** Codex runs documentation drift checks
- **THEN** the checks can compare that list with the files copied by `scripts/install-overlay` and required by `scripts/check-overlay`
- **AND** omissions are reported before release documentation is considered current

#### Scenario: Overlay check command path is spelled correctly
- **GIVEN** documentation tells users how to run the overlay checker
- **WHEN** Codex scans the documentation
- **THEN** references use `scripts/check-overlay`
- **AND** the obsolete spelling `script/check-overlay` is not present
