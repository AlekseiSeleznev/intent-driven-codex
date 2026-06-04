# overlay-update-safety Specification

## Purpose
Defines update and compatibility boundaries for the project-local overlay, persistent project context, and local secret files.
## Requirements
### Requirement: The template is an overlay on OpenSpec
The implementation MUST use OpenSpec public CLI behavior and project-local assets rather than modifying or forking OpenSpec.

#### Scenario: OpenSpec remains externally owned
- **GIVEN** the template is installed in a repository
- **WHEN** OpenSpec is upgraded by the user's package manager
- **THEN** the template files remain project-local
- **AND** OpenSpec core files are not patched by the template

#### Scenario: Existing artifacts are not overwritten by update checks
- **GIVEN** a repository contains active changes, canonical specs, or durable ADRs
- **WHEN** Codex runs overlay compatibility checks
- **THEN** existing project artifacts are read and validated
- **AND** they are not overwritten without explicit user approval

### Requirement: Overlay compatibility can be checked after OpenSpec updates
The template SHALL provide `/opsx:check-overlay` or an equivalent command that validates the installed overlay and expanded intent-driven lifecycle against the current OpenSpec CLI.

#### Scenario: Overlay check validates schema
- **GIVEN** the `intent-driven` schema is installed
- **WHEN** Codex runs overlay checks
- **THEN** Codex runs `openspec schema validate intent-driven`
- **AND** reports schema validation failures with the affected layer

#### Scenario: Overlay check uses valid OpenSpec status commands
- **GIVEN** OpenSpec 1.3.1 requires `--change` for `openspec status --json`
- **WHEN** Codex runs overlay checks
- **THEN** Codex uses `openspec list --json` or a temporary smoke change with `openspec status --change <name> --json`
- **AND** Codex does not run bare `openspec status --json`

#### Scenario: Overlay check validates expanded lifecycle smoke
- **GIVEN** the expanded `intent-driven` schema is installed
- **WHEN** Codex runs the full overlay smoke check
- **THEN** the smoke change creates proposal, specs, grill, design, design-review, ADR, test-plan, and tasks artifacts
- **AND** the apply instructions include `grill`, `design-review`, and `test-plan` context files before the smoke change is cleaned up

### Requirement: Compatibility failures identify the broken layer
The overlay check MUST report whether a failure belongs to OpenSpec CLI changes, local schema validation, Codex command adapters, generated skills, or project artifacts.

#### Scenario: Instructions command changes
- **GIVEN** OpenSpec changes the `openspec instructions` interface
- **WHEN** overlay checks detect the mismatch
- **THEN** Codex reports that the Codex command or skill adapter needs an update

### Requirement: Overlay updates preserve constitution and local secrets
Overlay update and compatibility workflows MUST treat `CONSTITUTION.md` as project-owned rules, `CONTEXT.md`, `ARCHITECTURE.md`, and `adr/` as project-owned architecture context, and `.secrets.local.env` as local-only secret state that must not be overwritten, archived, or exposed.

#### Scenario: Overlay update preserves constitution
- **GIVEN** a repository contains `CONSTITUTION.md`
- **WHEN** Codex installs or updates the intent-driven overlay
- **THEN** Codex does not overwrite the constitution without explicit user approval
- **AND** Codex reports any proposed constitution template changes separately from OpenSpec artifacts

#### Scenario: Overlay update detects legacy constitution
- **GIVEN** a repository contains legacy `PROJECT_CONSTITUTION.md`
- **AND** `CONSTITUTION.md` is missing
- **WHEN** Codex installs or updates the intent-driven overlay
- **THEN** Codex reports a migration opportunity
- **AND** Codex does not silently create a competing `CONSTITUTION.md` with different rules

#### Scenario: Overlay update preserves architecture context
- **GIVEN** a repository contains `CONTEXT.md`, `CONTEXT-MAP.md`, `ARCHITECTURE.md`, `adr/`, or `docs/architecture/`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not overwrite those project-owned files without explicit user approval
- **AND** Codex reports architecture template changes separately from OpenSpec artifacts

#### Scenario: Overlay update ignores local secret values
- **GIVEN** a repository contains `.secrets.local.env`
- **WHEN** Codex installs, updates, checks, archives, or reports overlay state
- **THEN** Codex does not print secret values
- **AND** Codex does not stage or commit `.secrets.local.env`

#### Scenario: Overlay health check can run without constitution or architecture
- **GIVEN** `CONSTITUTION.md`, `CONTEXT.md`, or `ARCHITECTURE.md` is missing
- **WHEN** Codex runs overlay health diagnostics
- **THEN** Codex may continue the diagnostic check
- **AND** Codex reports the missing file as project setup guidance rather than an OpenSpec CLI failure

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


### Requirement: Installed overlay checks avoid source-only false requirements
Overlay verification SHALL distinguish source-repository regression checks from installed-project health checks so installed projects are not required to carry source-only assets unless those assets are explicitly installed by the template.

#### Scenario: Source repository runs source regression suite
- **GIVEN** the intent-driven source repository contains the source regression helper `scripts/test-auto-repair-tooling`
- **WHEN** the source repository runs `scripts/check-overlay --no-smoke` or full `scripts/check-overlay`
- **THEN** source-only auto-repair regression checks run or are available as part of the source overlay check
- **AND** failures identify the auto-repair tooling layer

#### Scenario: Installed project does not need source-only suite
- **GIVEN** an installed project has all required installed overlay files but does not carry source-only regression assets
- **WHEN** the project runs `scripts/check-overlay --no-smoke`
- **THEN** the check passes if installed-project health requirements are satisfied
- **AND** any skipped source-only regression suite is reported as a source-only check rather than a project failure

### Requirement: Repair documentation layout checks accept canonical and alias docs
Auto-repair tooling SHALL accept the source repository's canonical docs and installed projects' alias docs as intentional layouts, while still detecting missing or drifted documentation in the applicable layout.

#### Scenario: Auto-repair helper accepts installed aliases
- **GIVEN** a project has `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md`
- **AND** it does not have `docs/lifecycle.md` or `docs/update-safety.md`
- **WHEN** `scripts/test-auto-repair-tooling doctor-doc-layouts` runs against an installed-style layout
- **THEN** the helper treats the alias docs as valid installed documentation
- **AND** it does not require canonical docs in the installed project

#### Scenario: Auto-repair helper accepts source canonical docs
- **GIVEN** the source repository has `docs/lifecycle.md` and `docs/update-safety.md`
- **AND** it does not have installed alias docs
- **WHEN** `scripts/test-auto-repair-tooling doctor-doc-layouts` runs against the source layout
- **THEN** the helper treats canonical docs as valid source documentation
- **AND** it does not require installed aliases in the source repository

### Requirement: Parent Git worktree targets are diagnosed
Install, repair, or overlay health tooling SHALL warn when the target directory is inside a parent Git worktree but is not itself the Git root, because Git status, tracked-secret checks, and ignore checks may otherwise refer to the parent repository.

#### Scenario: Target is nested under a parent Git root
- **GIVEN** a target directory is not a Git root
- **AND** a parent directory is a Git worktree root
- **WHEN** install, repair, or overlay diagnostics run for the target
- **THEN** the tooling prints a non-secret warning identifying that the target is nested under a parent Git worktree
- **AND** the warning explains whether checks will treat the target directory or the parent Git root as the working tree

#### Scenario: Target is its own Git root
- **GIVEN** a target directory is itself the Git root
- **WHEN** install, repair, or overlay diagnostics run for the target
- **THEN** no parent Git warning is printed
- **AND** normal Git status, ignore, and tracked-secret diagnostics still run
