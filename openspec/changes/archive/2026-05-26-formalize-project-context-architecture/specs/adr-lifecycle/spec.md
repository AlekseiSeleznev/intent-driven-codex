## ADDED Requirements

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

## MODIFIED Requirements

### Requirement: ADR review links change artifacts to durable decisions
The per-change ADR review SHALL list evaluated decisions, in-force ADRs considered, new durable ADRs created, any superseded ADRs, and whether `ARCHITECTURE.md` must be updated.

#### Scenario: Tasks read ADR review and durable ADRs
- **GIVEN** a change has `adr.md` and top-level ADR files
- **WHEN** Codex creates `tasks.md`
- **THEN** Codex reads the per-change ADR review
- **AND** Codex reads the relevant in-force durable ADRs before writing tasks
- **AND** Codex includes an architecture snapshot update task when the ADR review requires one
