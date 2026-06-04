## ADDED Requirements

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
