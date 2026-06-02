## ADDED Requirements

### Requirement: Installed projects are self-checking after repair
Installed projects SHALL receive every overlay-owned file that `scripts/check-overlay --no-smoke` requires through ordinary install, update, or `opsx repair` paths, without undocumented manual copying.

#### Scenario: Fresh installed project passes no-smoke overlay check
- **GIVEN** a fresh repository has run `openspec init . --tools codex --profile core`
- **WHEN** the intent-driven overlay is installed or repaired through the documented install/update path
- **THEN** `scripts/check-overlay --no-smoke` passes without manual copies
- **AND** required auto-repair tooling such as `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, and the repair regression helper required by the checker are present or the checker clearly treats the helper as source-only

#### Scenario: Brownfield alias-doc project passes no-smoke overlay check
- **GIVEN** an installed brownfield-style project contains installed documentation aliases `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md`
- **AND** it does not contain canonical source documentation paths `docs/lifecycle.md` or `docs/update-safety.md`
- **WHEN** the overlay is repaired through the documented update path
- **THEN** `scripts/check-overlay --no-smoke` passes without copying canonical source docs into the project
- **AND** the alias documentation remains the checked installed-project layout

### Requirement: Install and repair paths seed auto-repair tooling consistently
The installer and auto-repair manifest SHALL include the template-owned tooling needed by installed projects for repair diagnostics, doctor checks, and overlay verification while preserving project-owned context files as create-only assets.

#### Scenario: Installer copies auto-repair tooling
- **GIVEN** a target project runs `scripts/install-overlay <target> --force-overlay`
- **WHEN** installation completes
- **THEN** the target contains executable `bin/opsx` and `bin/openspec-shim`
- **AND** the target contains `manifest.yaml`
- **AND** the target contains `scripts/test-auto-repair-tooling` when installed overlay checks or diagnostics depend on it

#### Scenario: Manifest repairs auto-repair tooling
- **GIVEN** an installed project is missing one or more template-owned auto-repair tooling files
- **WHEN** `opsx repair --yes --root <project> --source-root <share>` runs from a complete share source
- **THEN** repair restores the missing template-owned tooling files listed in the manifest
- **AND** repair does not overwrite project-owned `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, or `adr/README.md` when those files already exist
