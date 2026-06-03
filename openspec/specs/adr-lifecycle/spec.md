# adr-lifecycle Specification

## Purpose
Defines per-change ADR review, durable append-only ADR history, and the relationship between in-force ADRs and the current architecture snapshot.

## Requirements

### Requirement: Every change has ADR review
The intent-driven workflow MUST create `openspec/changes/<change>/adr.md` for every change after mandatory grill and design-review gates, even when no durable ADR is needed.

#### Scenario: No durable ADR is needed
- **GIVEN** a change introduces no long-term architectural decision
- **AND** `grill.md` and `design-review.md` have resolved or explicitly deferred material findings
- **WHEN** Codex reaches the ADR step
- **THEN** Codex writes an ADR review explaining why no durable ADR is needed
- **AND** OpenSpec marks the ADR artifact complete

#### Scenario: Durable ADR is needed
- **GIVEN** a design or grill finding introduces a long-term architectural commitment
- **WHEN** Codex reaches the ADR step
- **THEN** Codex writes the per-change ADR review
- **AND** Codex creates a new durable ADR in top-level `adr/`

### Requirement: Durable ADR history is append-only
The workflow MUST preserve accepted ADR files as historical records and record later changes by creating new superseding ADRs instead of rewriting old ADR content.

#### Scenario: Decision supersedes previous ADR
- **GIVEN** an accepted ADR is currently in force
- **WHEN** a later change intentionally replaces that decision
- **THEN** Codex creates a new durable ADR that names the superseded ADR
- **AND** Codex does not edit the prior ADR body or rationale

#### Scenario: Current decisions are derived from supersession graph
- **GIVEN** the repository contains multiple durable ADRs
- **WHEN** Codex prepares design, tasks, apply, or verify context
- **THEN** Codex determines the in-force ADR set by following supersession links

### Requirement: ADR review links change artifacts to durable decisions
The per-change ADR review SHALL list evaluated decisions, grill/design-review findings considered, in-force ADRs considered, new durable ADRs created, any superseded ADRs, and whether `ARCHITECTURE.md` must be updated.

#### Scenario: Tasks read ADR review and durable ADRs
- **GIVEN** a change has `grill.md`, `design-review.md`, `adr.md`, and top-level ADR files
- **WHEN** Codex creates `tasks.md`
- **THEN** Codex reads the grill gates and ADR review
- **AND** Codex reads the relevant in-force durable ADRs before writing tasks
- **AND** Codex includes an architecture snapshot update task when the ADR review requires one

#### Scenario: ADR records grill-driven decisions
- **GIVEN** `grill.md` or `design-review.md` identifies a material decision that affects architecture
- **WHEN** Codex creates `adr.md`
- **THEN** the ADR review records how that finding was resolved
- **AND** it identifies whether a durable top-level ADR is required

### Requirement: Architecture snapshot reflects in-force ADRs
The workflow SHALL keep `ARCHITECTURE.md` as a current snapshot derived from accepted in-force durable ADRs rather than as an independent competing decision log.

#### Scenario: Durable ADR changes architecture
- **GIVEN** a change creates a new durable ADR or supersedes an existing durable ADR
- **WHEN** Codex prepares tasks for that change
- **THEN** the tasks include updating `ARCHITECTURE.md` when the current architecture snapshot is affected
- **AND** the snapshot links to the relevant ADR files

#### Scenario: Codex needs current decisions
- **GIVEN** Codex starts architecture-sensitive planning or implementation
- **WHEN** `ARCHITECTURE.md` references in-force ADRs
- **THEN** Codex reads the referenced ADR files when detailed rationale or constraints are needed
- **AND** Codex does not rewrite accepted ADR history to update the snapshot
