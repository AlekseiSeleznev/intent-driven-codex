# test-first-verification Specification

## Purpose
Defines mandatory Matt TDD planning, RED/GREEN/REFACTOR evidence, and verification behavior for intent-driven changes.

## Requirements

### Requirement: Development changes include a TDD plan
Every development or enhancement change MUST include `openspec/changes/<change>/test-plan.md` before implementation tasks are created.

#### Scenario: Test plan maps requirements to TDD slices
- **GIVEN** a change has delta specs with requirements and scenarios
- **WHEN** Codex creates `test-plan.md`
- **THEN** the test plan maps each behavior to a vertical RED -> GREEN -> REFACTOR slice
- **AND** the plan identifies required checks before apply completion and before archive

#### Scenario: Test plan documents approved exceptions
- **GIVEN** classic automated TDD is not feasible for the change or technology stack
- **WHEN** Codex creates `test-plan.md`
- **THEN** the plan documents the blocker and strongest substitute evidence as an explicit exception
- **AND** it does not treat unverified work as complete

### Requirement: Matt TDD is canonical for apply implementation
The overlay MUST vendor the Matt Pocock `tdd` skill as the canonical project-local TDD discipline for implementation work, while allowing other TDD skills only as compatible supplements.

#### Scenario: Apply starts with a failing behavior check
- **GIVEN** a change contains implementation tasks that alter behavior
- **WHEN** Codex begins `/opsx:apply <change>`
- **THEN** Codex writes or identifies one behavior-focused public-interface test/check before production code for the current slice
- **AND** Codex runs it and records RED evidence before writing the implementation for that slice

#### Scenario: Apply uses vertical slices
- **GIVEN** a test plan lists multiple behaviors
- **WHEN** Codex implements the change
- **THEN** Codex follows one RED -> GREEN -> REFACTOR slice at a time
- **AND** Codex does not write all tests first and then all implementation as a horizontal slice

#### Scenario: Production code before RED is invalid
- **GIVEN** Codex wrote production code for a behavior before observing the related failing test/check
- **WHEN** Codex detects the ordering violation
- **THEN** Codex must stop and restart that slice from RED, or report a critical TDD exception that requires explicit user approval

### Requirement: Apply follows the test plan before marking tasks complete
The apply workflow MUST use `test-plan.md` as implementation context and MUST not mark tasks complete without RED/GREEN evidence or an approved exception.

#### Scenario: Apply reads test plan
- **GIVEN** a change is apply-ready
- **WHEN** Codex runs `/opsx:apply <change>`
- **THEN** Codex reads `test-plan.md` from OpenSpec context files
- **AND** Codex uses it to order test-first and implementation tasks

#### Scenario: Verification limitation blocks silent completion
- **GIVEN** a required check from `test-plan.md` cannot run
- **WHEN** Codex reports apply or verify status
- **THEN** Codex reports the blocker or limitation explicitly
- **AND** Codex does not silently mark the associated verification complete

### Requirement: Verification blocks missing TDD evidence
The `/opsx:verify` workflow MUST treat missing RED/GREEN evidence for behavior changes as a critical issue unless an explicit test-plan exception was approved before apply.

#### Scenario: Red evidence is missing
- **GIVEN** tasks are checked complete for a behavior-changing slice
- **WHEN** verification inspects `test-plan.md` and implementation evidence
- **THEN** missing RED evidence is reported as a critical issue

#### Scenario: Green evidence is missing
- **GIVEN** a TDD slice has RED evidence but no passing GREEN evidence
- **WHEN** verification runs
- **THEN** missing GREEN evidence is reported as a critical issue
