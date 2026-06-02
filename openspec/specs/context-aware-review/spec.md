# context-aware-review Specification

## Purpose
Defines Matt `grill-with-docs` gates, glossary context, scenario quality, diagram guidance, and use of persistent project context during review.

## Requirements

### Requirement: Matt grill-with-docs is the canonical review discipline
Codex MUST use the project-local Matt Pocock `grill-with-docs` skill for `grill.md` and `design-review.md` gates, adapted to the project context model.

#### Scenario: Grill runs automatically at artifact gates
- **GIVEN** `grill` or `design-review` is the next ready artifact
- **WHEN** Codex continues or fast-forwards a change
- **THEN** Codex runs the `grill-with-docs` workflow without asking whether to run it
- **AND** Codex asks the user only when a material uncertainty cannot be resolved from repository context

#### Scenario: Grill asks focused questions
- **GIVEN** material uncertainty remains after reading docs and code
- **WHEN** Codex grills the plan
- **THEN** Codex asks one focused question at a time
- **AND** Codex includes its recommended answer and rationale
- **AND** Codex waits for that answer before moving to the next question

#### Scenario: Low-risk grill remains concise
- **GIVEN** relevant documents and code reveal no material uncertainty
- **WHEN** Codex creates `grill.md` or `design-review.md`
- **THEN** the artifact records that no material questions remain
- **AND** the workflow does not ask ceremonial questions only to satisfy the gate

### Requirement: Grill updates project docs inline
Codex MUST integrate material grill resolutions into the appropriate persistent context or OpenSpec artifacts before dependent gates consume them.

#### Scenario: Glossary term is resolved
- **GIVEN** a domain term or language ambiguity is resolved during grill
- **WHEN** the resolution affects project language
- **THEN** Codex updates `CONTEXT.md` or the relevant context-local glossary
- **AND** Codex keeps implementation details out of the glossary

#### Scenario: Artifact update is required
- **GIVEN** grill finds a contradiction with proposal, specs, design, or ADR constraints
- **WHEN** Codex records the grill result
- **THEN** Codex updates the affected artifact or records the exact required update
- **AND** the next gate remains blocked while material open questions remain

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

### Requirement: Specs use Gherkin-style observable scenarios
The workflow MUST apply Gherkin authoring guidance to OpenSpec Markdown specs so scenarios describe domain behavior and observable outcomes.

#### Scenario: Scenario uses domain language
- **GIVEN** Codex drafts a requirement scenario
- **WHEN** the behavior is user-observable
- **THEN** the scenario describes initial state, event, and outcome in domain terms
- **AND** it avoids private implementation details unless they are part of the contract

### Requirement: Design uses diagram guidance when boundaries are non-trivial
The workflow SHALL use diagramming guidance when a change affects multiple components, services, integrations, runtime units, deployment boundaries, project context architecture, or durable architecture summaries.

#### Scenario: Design includes minimal useful view
- **GIVEN** a change affects more than one runtime or architectural boundary
- **WHEN** Codex creates `design.md`
- **THEN** the design contains the smallest useful ASCII or Mermaid diagram
- **AND** assumptions and open questions are explicitly listed

#### Scenario: Simple change records no diagram needed
- **GIVEN** a change is local and has no meaningful architecture boundary
- **WHEN** Codex creates `design.md`
- **THEN** the design explicitly records that no diagram is needed and why
