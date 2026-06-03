## ADDED Requirements

### Requirement: Matt grill-with-docs is the canonical review discipline
Codex MUST use the project-local Matt Pocock `grill-with-docs` skill for `grill.md` and `design-review.md` gates, adapted to the project context model.

#### Scenario: Grill runs automatically at its artifact gates
- **GIVEN** `grill` or `design-review` is the next ready artifact
- **WHEN** Codex continues or fast-forwards a change
- **THEN** Codex runs the `grill-with-docs` workflow without asking whether to run it
- **AND** Codex asks the user only when a material uncertainty cannot be resolved from repository context

#### Scenario: Grill asks one question at a time
- **GIVEN** material uncertainty remains after reading docs and code
- **WHEN** Codex grills the plan
- **THEN** Codex asks one focused question
- **AND** Codex includes its recommended answer and rationale
- **AND** Codex waits for that answer before moving to the next question

#### Scenario: Grill updates project docs inline
- **GIVEN** a domain term, ambiguity, decision, or artifact mismatch is resolved during grill
- **WHEN** the resolution affects persistent context or OpenSpec planning
- **THEN** Codex updates the appropriate `CONTEXT.md`, proposal, specs, design, ADR review, durable ADR, test plan, or tasks document before proceeding

### Requirement: Context glossary is separate from policy and architecture
Codex MUST treat `CONTEXT.md` as a glossary/domain-language file, not as project policy, architecture, implementation design, or a scratchpad.

#### Scenario: Root context is used for single-context repositories
- **GIVEN** a repository has root `CONTEXT.md` and no `CONTEXT-MAP.md`
- **WHEN** Codex runs grill or TDD planning
- **THEN** Codex uses root `CONTEXT.md` for project language and term ambiguity checks

#### Scenario: Context map is used for multi-context repositories
- **GIVEN** a repository has root `CONTEXT-MAP.md`
- **WHEN** Codex runs grill or TDD planning
- **THEN** Codex uses the map to select relevant context-local `CONTEXT.md` and ADR files
- **AND** if the relevant context is unclear, Codex asks one clarifying question
