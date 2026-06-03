## ADDED Requirements

### Requirement: Auto-repair installer preserves the selected share source
The `opsx install-auto-repair` command SHALL never delete the source tree it is about to install from. When the resolved source root and share root are the same directory, installation SHALL preserve that directory and still install executable shims safely.

#### Scenario: Source root equals share root
- **GIVEN** `opsx install-auto-repair --source-root <root> --share-root <root>` is invoked
- **AND** `<root>` contains the repair manifest and shim source files
- **WHEN** installation runs
- **THEN** `<root>` is not removed or replaced before copying executables
- **AND** the command installs or refreshes `opsx` and the OpenSpec shim in the requested bin directory
- **AND** the persisted state records the preserved share root

#### Scenario: Different source and share roots still refresh share copy
- **GIVEN** `opsx install-auto-repair --source-root <source> --share-root <share>` is invoked with different resolved directories
- **WHEN** installation runs
- **THEN** the share root is refreshed from the source root using the documented ignore rules
- **AND** the command installs or refreshes `opsx` and the OpenSpec shim in the requested bin directory

### Requirement: Install checks detect stale real OpenSpec paths
The auto-repair install/update tooling SHALL include a post-install sanity check that compares the shim-resolved real OpenSpec binary with the current real OpenSpec CLI and verifies both report the same current version. If the state file points at a stale or missing binary, the check SHALL fail clearly before reporting installation success.

#### Scenario: Real OpenSpec path is current
- **GIVEN** auto-repair installation records a real OpenSpec binary path
- **WHEN** the post-install sanity check runs
- **THEN** invoking the installed shim for `--version` reports the same version as invoking the recorded real OpenSpec binary directly
- **AND** the command reports the checked real OpenSpec path

#### Scenario: Real OpenSpec path is stale
- **GIVEN** the auto-repair state points at an old, missing, or different real OpenSpec binary
- **WHEN** the post-install sanity check runs
- **THEN** the check fails with a clear stale-real-OpenSpec diagnostic
- **AND** the user is told which non-secret path must be refreshed
- **AND** the command does not claim the install/update check passed
