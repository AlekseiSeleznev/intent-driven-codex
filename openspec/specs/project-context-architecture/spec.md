# project-context-architecture Specification

## Purpose
Defines persistent project context entry points that make current architecture and project rules available to Codex workflows without turning them into OpenSpec change artifacts.
## Requirements
### Requirement: Current architecture snapshot is root project context
The template SHALL provide a root-level `ARCHITECTURE.md` file as Git-tracked current architecture context that Codex reads for architecture-sensitive planning, design, implementation, verification, and archive work.

#### Scenario: New chat reads current architecture
- **GIVEN** a repository contains `ARCHITECTURE.md` at the project root
- **WHEN** Codex starts a new architecture-sensitive task in the project
- **THEN** Codex reads `ARCHITECTURE.md` before relying on architecture assumptions
- **AND** Codex uses it as the current architecture snapshot for the task

#### Scenario: Architecture snapshot links durable decisions
- **GIVEN** the repository contains accepted durable ADRs under `adr/`
- **WHEN** Codex reads `ARCHITECTURE.md`
- **THEN** the file identifies or links the current in-force ADR set
- **AND** Codex can navigate from the snapshot to detailed ADR files when the task needs decision rationale

### Requirement: OpenSpec bridge indexes persistent project context
The repository SHALL provide an OpenSpec bridge document that explains persistent project context files outside the OpenSpec artifact graph.

#### Scenario: User opens OpenSpec directory
- **GIVEN** a user or Codex inspects `openspec/`
- **WHEN** `openspec/README.md` exists
- **THEN** it points to root `CONSTITUTION.md`, root `CONTEXT.md` or `CONTEXT-MAP.md`, root `ARCHITECTURE.md`, `adr/`, and relevant `docs/` paths
- **AND** it states that those files are project context, not `openspec/changes/` artifacts

#### Scenario: OpenSpec archive preserves project context
- **GIVEN** a completed change is archived
- **WHEN** Codex runs archive or sync work
- **THEN** root `CONSTITUTION.md`, root `CONTEXT.md`, root `CONTEXT-MAP.md`, root `ARCHITECTURE.md`, `adr/`, and `.secrets.local.env` are not moved into `openspec/changes/archive/`
- **AND** Codex treats them as persistent project context or local-only state according to their file role

### Requirement: Secrets stay outside architecture and OpenSpec context
Architecture and OpenSpec context files MUST NOT contain secret values and SHALL reference only non-secret variable names when external systems are relevant.

#### Scenario: Architecture references external systems
- **GIVEN** `ARCHITECTURE.md` describes an external integration or deployment boundary
- **WHEN** credentials or private service details are needed for that integration
- **THEN** the architecture document references variable names or non-secret labels only
- **AND** real values remain in `.secrets.local.env` or the local environment

### Requirement: Project context includes glossary entry points
The project context model SHALL include optional `CONTEXT.md` and `CONTEXT-MAP.md` glossary entry points alongside `CONSTITUTION.md`, `ARCHITECTURE.md`, and `adr/`.

#### Scenario: Constitution remains policy
- **GIVEN** a repository has both `CONSTITUTION.md` and `CONTEXT.md`
- **WHEN** Codex reads project context
- **THEN** `CONSTITUTION.md` is used for project rules, secrets policy, MCP, external systems, docs, and verification rules
- **AND** `CONTEXT.md` is used only for domain language and glossary terms

#### Scenario: Architecture remains current snapshot
- **GIVEN** a repository has `ARCHITECTURE.md`, `CONTEXT.md`, and top-level ADRs
- **WHEN** Codex performs architecture-sensitive work
- **THEN** `ARCHITECTURE.md` remains the current architecture snapshot
- **AND** `CONTEXT.md` does not replace ADRs or architecture decisions

