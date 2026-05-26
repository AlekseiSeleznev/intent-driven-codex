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
- `../ARCHITECTURE.md` — current architecture snapshot for new chats.
- `../adr/` — append-only durable architecture decisions.
- `../docs/` — supporting documentation.
- `../.secrets.example.env` — tracked variable-name examples only.

Local secret values belong only in `../.secrets.local.env` or the local
environment. That file is ignored by Git, must not be archived, and must not be
printed or copied into OpenSpec artifacts.

OpenSpec CLI does not read the constitution or architecture snapshot by itself.
The Codex layer (`.codex/prompts` and `.codex/skills`) is responsible for that
preflight.
