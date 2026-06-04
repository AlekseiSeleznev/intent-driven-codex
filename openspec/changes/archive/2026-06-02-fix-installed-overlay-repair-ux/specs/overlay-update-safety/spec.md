## ADDED Requirements

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
