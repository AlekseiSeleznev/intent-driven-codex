# OpenSpec bridge

This directory contains OpenSpec lifecycle state for the project. It is not the
only context Codex must read.

## OpenSpec-owned lifecycle context

- `config.yaml` — selects the default schema (`intent-driven`).
- `schemas/intent-driven/` — project-local schema and artifact templates.
- `specs/` — canonical behavior specifications after archive/sync.
- `changes/` — active and archived OpenSpec changes.

## Persistent project context outside OpenSpec artifacts

Codex workflows must also use these root-level files when relevant:

- `../CONSTITUTION.md` — persistent project rules, no secret values.
- `../CONTEXT.md` — glossary/domain language only.
- `../CONTEXT-MAP.md` — optional map for multi-context repositories.
- `../ARCHITECTURE.md` — current architecture snapshot for new chats.
- `../adr/` — append-only durable architecture decisions.
- `../docs/` — supporting documentation.
- `../.secrets.example.env` — tracked variable-name examples only.

Local secret values belong only in `../.secrets.local.env` or the local
environment. That file is ignored by Git, must not be archived, and must not be
printed or copied into OpenSpec artifacts.

OpenSpec CLI does not read constitution, glossary, or architecture snapshot
files by itself. The Codex layer (`.codex/prompts` and `.codex/skills`) is
responsible for that preflight.

## Intent-driven artifact order

The current project-local lifecycle is:

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks
```

`grill.md` is the mandatory pre-design Matt `grill-with-docs` gate.
`design-review.md` is the mandatory post-design Matt `grill-with-docs` gate.
`test-plan.md` is the mandatory Matt TDD planning gate. These are OpenSpec
change artifacts; root `CONSTITUTION.md`, `CONTEXT.md`, `CONTEXT-MAP.md`, root
`ARCHITECTURE.md`, top-level `adr/`, and local secrets remain outside the change
artifact graph.
