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
- `adr/0006-adopt-claude-artifact-review.md` — adds optional Claude Code
  print-mode artifact review as a Codex-overlay reviewer gate with pinned-model-first stage configuration.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` —
  adopts automatic safe lifecycle checkpoints by default and session-scoped
  Claude review controls; it narrows ADR 0001 only for ordinary local
  lifecycle checkpoint commits.

ADR 0002 is superseded by ADR 0003. ADR 0004 is superseded by ADR 0005. Both
remain historical context. ADR 0001 remains otherwise in force; ADR 0007 narrows only its checkpoint-approval rule for safe local lifecycle commits.

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
  Opsx --> SessionState[".codex/session/openspec-session.json\nignored non-secret session state"]
  Opsx --> GitCheckpoint["scripts/openspec-git-checkpoint\nscoped automatic checkpoint commits"]
  GitCheckpoint --> GitCLI["Git CLI\nlocal checkpoint commits"]
  Opsx --> ClaudeReview["scripts/openspec-claude-review\noptional artifact reviewer"]
  ClaudeReview --> ReviewConfig[".codex/openspec-claude-review.json\nnon-secret stage config"]
  SessionState --> ReviewConfig
  ClaudeReview --> ClaudeCLI["claude -p\nexternal local Claude Code"]
  ClaudeReview --> ReviewReports["openspec/changes/<change>/reviews/*.json\nauxiliary review reports"]
  Opsx --> OpenSpec["OpenSpec CLI\npublic lifecycle engine"]
  OpenSpec --> Changes["openspec/changes/<change>\nproposal/specs/grill/design/design-review/adr/test-plan/tasks"]
  Changes --> ReviewReports
  OpenSpec --> Specs["openspec/specs\ncanonical behavior"]

  classDef codex fill:#dff3ff,stroke:#1d75b9,color:#042033,stroke-width:2px;
  classDef context fill:#fff7ed,stroke:#ea580c,color:#431407,stroke-width:2px;
  classDef secret fill:#fee2e2,stroke:#dc2626,color:#450a0a,stroke-width:2px;
  classDef openspec fill:#e8fff5,stroke:#16885a,color:#06291b,stroke-width:2px;
  class Opsx,Preflight,Grill,TDD,ClaudeReview,GitCheckpoint codex;
  class Constitution,Glossary,Architecture,ADRs,ReviewConfig,SessionState context;
  class Secrets secret;
  class OpenSpec,Changes,Specs,ReviewReports openspec;
  class ClaudeCLI secret;
  class GitCLI codex;
```

## Boundaries

- **Codex overlay** (`.codex/prompts`, `.codex/skills`) owns workflow behavior,
  project-context preflight, Matt grill gates, Matt TDD apply discipline, optional
  Claude artifact review orchestration, session review controls, scoped automatic
  checkpoint helpers, goal hand-off prompts, quality skills, and Git discipline
  guidance.
- **OpenSpec CLI** owns lifecycle state, artifact dependency ordering,
  instructions, validation, and archive mechanics. It does not read
  `CONSTITUTION.md`, `CONTEXT.md`, or `ARCHITECTURE.md` itself.
- **Root project context** (`CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`,
  `adr/`, and selected `docs/`) is persistent Git-tracked context outside
  OpenSpec change artifacts.
- **Local secrets** (`.secrets.local.env`) are local-only values outside Git and
  outside the OpenSpec archive flow. Claude authentication is also external local
  state and must not be copied into tracked review config or reports.

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

Safe OpenSpec lifecycle checkpoints are automatic by default when session git
discipline is `auto`. Codex still shows affected paths, commit message, commit
hash, and post-commit status. Manual mode remains available, and push, merge,
pull request creation, archive, destructive Git operations, dirty unrelated work,
and hard-gate bypasses still require explicit approval.

Claude review enablement and stage settings can be overlaid per session through
ignored non-secret `.codex/session/openspec-session.json`; the overlay merges
over `.codex/openspec-claude-review.json` and never stores credentials.

When `.codex/openspec-claude-review.json` enables a stage, Codex can call
`scripts/openspec-claude-review` after generating that artifact. The helper uses
`claude -p` with configured model, effort, optional budget cap, and structured output, then
writes auxiliary reports under `openspec/changes/<change>/reviews/`. These
reports inform later gates and verify, but they do not replace any OpenSpec
artifact, grill gate, ADR review, TDD evidence, or validation check.
If Claude Code reports budget exhaustion, the helper blocks the lifecycle step,
reports the budget blocker, and disables Claude review in ignored session state
to prevent repeated reviewer calls until the user explicitly re-enables it.

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
