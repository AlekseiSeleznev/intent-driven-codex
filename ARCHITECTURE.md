# Architecture Snapshot

This file is the current architecture entry point for new Codex chats and
architecture-sensitive `/opsx:*` work. It summarizes the in-force state; durable
rationale lives in `adr/`.

## Current in-force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — adopts the
  project-local Codex/OpenSpec overlay architecture.
- `adr/0003-formalize-project-context-entrypoints.md` — formalizes root
  `CONSTITUTION.md`, root `ARCHITECTURE.md`, `openspec/README.md` as a bridge,
  ADR-derived architecture snapshots, and local-secret boundaries.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — hardens the lifecycle with
  Matt `grill-with-docs` gates, glossary context, and canonical Matt TDD apply
  discipline.

ADR 0002 is superseded by ADR 0003. ADR 0004 is superseded by ADR 0005. Both
remain historical context.

## System model

```mermaid
flowchart LR
  User["User / Codex chat"] --> Opsx["/opsx:* prompts\nand openspec-* skills"]
  Opsx --> Preflight["Project context preflight\nCodex layer"]
  Preflight --> Constitution["CONSTITUTION.md\nproject rules, no secrets"]
  Preflight --> Glossary["CONTEXT.md / CONTEXT-MAP.md\nglossary only"]
  Preflight --> Architecture["ARCHITECTURE.md\ncurrent snapshot"]
  Architecture --> ADRs["adr/\ndurable decision history"]
  Preflight --> Secrets[".secrets.local.env\nlocal values only when needed"]
  Opsx --> Grill["grill-with-docs\none question at a time"]
  Opsx --> TDD["tdd\nRED → GREEN → REFACTOR"]
  Opsx --> OpenSpec["OpenSpec CLI\npublic lifecycle engine"]
  OpenSpec --> Changes["openspec/changes/<change>\nproposal/specs/grill/design/design-review/adr/test-plan/tasks"]
  OpenSpec --> Specs["openspec/specs\ncanonical behavior"]

  classDef codex fill:#dff3ff,stroke:#1d75b9,color:#042033,stroke-width:2px;
  classDef context fill:#fff7ed,stroke:#ea580c,color:#431407,stroke-width:2px;
  classDef secret fill:#fee2e2,stroke:#dc2626,color:#450a0a,stroke-width:2px;
  classDef openspec fill:#e8fff5,stroke:#16885a,color:#06291b,stroke-width:2px;
  class Opsx,Preflight,Grill,TDD codex;
  class Constitution,Glossary,Architecture,ADRs context;
  class Secrets secret;
  class OpenSpec,Changes,Specs openspec;
```

## Boundaries

- **Codex overlay** (`.codex/prompts`, `.codex/skills`) owns workflow behavior,
  project-context preflight, Matt grill gates, Matt TDD apply discipline, goal
  hand-off prompts, quality skills, and Git discipline guidance.
- **OpenSpec CLI** owns lifecycle state, artifact dependency ordering,
  instructions, validation, and archive mechanics. It does not read
  `CONSTITUTION.md`, `CONTEXT.md`, or `ARCHITECTURE.md` itself.
- **Root project context** (`CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`,
  `adr/`, and selected `docs/`) is persistent Git-tracked context outside
  OpenSpec change artifacts.
- **Local secrets** (`.secrets.local.env`) are local-only values outside Git and
  outside the OpenSpec archive flow.

## Lifecycle

The canonical OpenSpec lifecycle is:

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks -> apply -> verify -> archive
```

`grill.md` records mandatory pre-design Matt `grill-with-docs` findings.
`design-review.md` records mandatory post-design Matt `grill-with-docs` findings.
Both gates ask one material question at a time only when repository context
cannot answer the uncertainty, and both must reach `Open Questions: None` or an
explicit override before the next gate consumes them.

`test-plan.md` records vertical Matt TDD slices. `/opsx:apply` follows RED ->
GREEN -> REFACTOR one behavior at a time and must not write production code for
a behavior before RED evidence unless an explicit exception is approved in the
test plan.

Before `/opsx:*` workflows and direct lifecycle skill actions, Codex reads
`CONSTITUTION.md`. For terminology-sensitive work it reads `CONTEXT.md` or
`CONTEXT-MAP.md` when present. For architecture-sensitive work it also reads
this file, `adr/README.md`, and relevant in-force `adr/*.md` files.

## Update rules

- Update `CONTEXT.md` when project glossary/domain language changes.
- Update `ARCHITECTURE.md` whenever an accepted durable ADR changes the current
  architecture snapshot.
- Do not rewrite accepted ADR bodies to change history; create a superseding ADR
  instead.
- Do not move `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/`, or
  `.secrets.local.env` into `openspec/changes/archive/`.
- Do not store real secret values in Git-tracked project context.
