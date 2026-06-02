## ADDED Requirements

### Requirement: Every change has ADR review
The intent-driven workflow MUST create `openspec/changes/<change>/adr.md` for every change, even when no durable ADR is needed.

#### Scenario: No durable ADR is needed
- **GIVEN** a change introduces no long-term architectural decision
- **WHEN** Codex reaches the ADR step
- **THEN** Codex writes an ADR review explaining why no durable ADR is needed
- **AND** OpenSpec marks the ADR artifact complete

#### Scenario: Durable ADR is needed
- **GIVEN** a design introduces a long-term architectural commitment
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
The per-change ADR review SHALL list evaluated decisions, in-force ADRs considered, new durable ADRs created, and any superseded ADRs.

#### Scenario: Tasks read ADR review and durable ADRs
- **GIVEN** a change has `adr.md` and top-level ADR files
- **WHEN** Codex creates `tasks.md`
- **THEN** Codex reads the per-change ADR review
- **AND** Codex reads the relevant in-force durable ADRs before writing tasks
