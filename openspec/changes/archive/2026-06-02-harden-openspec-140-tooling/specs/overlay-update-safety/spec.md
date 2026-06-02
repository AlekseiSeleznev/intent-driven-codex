## ADDED Requirements

### Requirement: Auto-repair runs only for mutating lifecycle commands
The OpenSpec shim SHALL delegate no-op, help, version, and read-only commands to the real OpenSpec CLI without invoking overlay auto-repair. Auto-repair SHALL run only after successful mutating commands that can create or update Codex/OpenSpec project files.

#### Scenario: Update help does not repair
- **GIVEN** a project has an installed intent-driven OpenSpec shim
- **WHEN** the user runs `openspec update --help`
- **THEN** the shim delegates to the real OpenSpec CLI help output
- **AND** `opsx repair` is not invoked
- **AND** no project files are created, replaced, or deleted by the shim

#### Scenario: Version command does not repair
- **GIVEN** a project has an installed intent-driven OpenSpec shim
- **WHEN** the user runs `openspec --version`
- **THEN** the shim prints the real OpenSpec CLI version
- **AND** `opsx repair` is not invoked
- **AND** no project files are created, replaced, or deleted by the shim

#### Scenario: Successful mutating update can repair
- **GIVEN** a project has an installed intent-driven OpenSpec shim
- **WHEN** the user runs a successful mutating OpenSpec update command without help or version flags
- **THEN** the shim invokes overlay auto-repair for the target project
- **AND** repair uses only manifest-listed template-owned or create-only files

### Requirement: Overlay diagnostics distinguish source and installed documentation
The `opsx doctor` and repair drift checks SHALL support both the source repository document layout and the installed project document alias layout without reporting false drift for canonical source docs.

#### Scenario: Source repository canonical docs are healthy
- **GIVEN** the intent-driven source repository contains canonical docs `docs/lifecycle.md` and `docs/update-safety.md`
- **AND** it does not contain installed aliases `docs/intent-driven-lifecycle.md` or `docs/intent-driven-update-safety.md`
- **WHEN** `opsx doctor --root <source-repo> --source-root <source-repo>` runs
- **THEN** doctor reports the overlay as healthy when all other manifest-owned files match
- **AND** it does not require the installed alias doc names in the source repository

#### Scenario: Installed project aliases remain checked
- **GIVEN** an installed project receives docs as `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md`
- **WHEN** `opsx doctor --root <installed-project>` runs
- **THEN** doctor checks the installed alias docs for drift
- **AND** it reports missing or changed installed alias docs as template-owned drift
