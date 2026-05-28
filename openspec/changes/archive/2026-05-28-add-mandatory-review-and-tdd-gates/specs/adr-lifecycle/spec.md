## MODIFIED Requirements

### Requirement: Every change has ADR review
The intent-driven workflow MUST create `openspec/changes/<change>/adr.md` for every change after the mandatory context-aware review, even when no durable ADR is needed.

#### Scenario: No durable ADR is needed
- **GIVEN** a change introduces no long-term architectural decision
- **AND** `review.md` has resolved or explicitly deferred material review findings
- **WHEN** Codex reaches the ADR step
- **THEN** Codex writes an ADR review explaining why no durable ADR is needed
- **AND** OpenSpec marks the ADR artifact complete

#### Scenario: Durable ADR is needed
- **GIVEN** a design or review introduces a long-term architectural commitment
- **WHEN** Codex reaches the ADR step
- **THEN** Codex writes the per-change ADR review
- **AND** Codex creates a new durable ADR in top-level `adr/`

### Requirement: ADR review links change artifacts to durable decisions
The per-change ADR review SHALL list evaluated decisions, review findings considered, in-force ADRs considered, new durable ADRs created, any superseded ADRs, and whether `ARCHITECTURE.md` must be updated.

#### Scenario: Tasks read ADR review and durable ADRs
- **GIVEN** a change has `review.md`, `adr.md`, and top-level ADR files
- **WHEN** Codex creates `tasks.md`
- **THEN** Codex reads the per-change review and ADR review
- **AND** Codex reads the relevant in-force durable ADRs before writing tasks
- **AND** Codex includes an architecture snapshot update task when the ADR review requires one

#### Scenario: ADR records review-driven decisions
- **GIVEN** `review.md` identifies a material decision that affects architecture
- **WHEN** Codex creates `adr.md`
- **THEN** the ADR review records how that review finding was resolved
- **AND** it identifies whether a durable top-level ADR is required
