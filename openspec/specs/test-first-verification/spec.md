# test-first-verification Specification

## Purpose
TBD - created by archiving change add-mandatory-review-and-tdd-gates. Update Purpose after archive.
## Requirements
### Requirement: Development changes include a test-first verification plan
Every development or enhancement change MUST include `openspec/changes/<change>/test-plan.md` before implementation tasks are created.

#### Scenario: Test plan maps requirements to verification
- **GIVEN** a change has delta specs with requirements and scenarios
- **WHEN** Codex creates `test-plan.md`
- **THEN** the test plan maps each requirement or scenario to an automated or manual verification method
- **AND** the plan identifies required checks before apply completion and before archive

#### Scenario: Test plan prefers test-first evidence
- **GIVEN** an automated or executable check can be written before production implementation
- **WHEN** Codex creates `test-plan.md`
- **THEN** the plan describes the expected failing check or test-first step before implementation
- **AND** later tasks include implementing or running that check before or alongside production changes

#### Scenario: Test plan documents verification-first substitute
- **GIVEN** classic unit-test-first TDD is not feasible for the change or technology stack
- **WHEN** Codex creates `test-plan.md`
- **THEN** the plan documents the strongest available verification-first substitute
- **AND** it explains the limitation without treating the change as unverified

### Requirement: Apply follows the test plan before marking tasks complete
The apply workflow MUST use `test-plan.md` as implementation context and MUST not mark tasks complete without running the mapped checks or reporting explicit verification limitations.

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

### Requirement: Verify reports test-plan coverage
The verify workflow SHALL compare implementation and evidence against `test-plan.md` as well as proposal, specs, design, ADR, and tasks.

#### Scenario: Verify includes test-plan evidence
- **GIVEN** a change has implementation changes and `test-plan.md`
- **WHEN** Codex runs `/opsx:verify <change>`
- **THEN** the verification report maps requirements and scenarios to checks or manual evidence from the test plan
- **AND** missing required checks are reported as critical issues unless explicitly accepted as limitations
