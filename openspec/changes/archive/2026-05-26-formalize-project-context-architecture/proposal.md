## Why

New Codex chats must be able to recover the current project architecture, active architecture decisions, and mandatory project rules without relying on transient conversation memory. Today those sources are split across `PROJECT_CONSTITUTION.md`, `adr/`, `docs/`, README sections, and OpenSpec specs, and the constitution filename is more verbose than the desired canonical `CONSTITUTION.md`.

## What Changes

- **BREAKING**: Rename the canonical project constitution file from `PROJECT_CONSTITUTION.md` to root `CONSTITUTION.md` and update all native intent-driven prompts, skills, docs, schema guidance, specs, and checks to use the new name.
- Add root `ARCHITECTURE.md` as the canonical current architecture snapshot that every new project chat can read quickly.
- Keep durable architecture decisions in `adr/`, with `ARCHITECTURE.md` summarizing the current in-force ADR set and linking to detailed ADR files.
- Add an OpenSpec bridge document under `openspec/README.md` that explains which project context lives outside `openspec/` and must be read by Codex workflows.
- Update `/opsx:*` and direct `openspec-*` skill guidance so architecture-sensitive work reads `CONSTITUTION.md`, `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md` before planning/apply/verify actions.
- Keep real secret values outside `openspec/` and outside Git in root `.secrets.local.env`; keep `.secrets.example.env` as tracked variable-name guidance only.
- Update install/update/check documentation so Brownfield projects preserve existing constitution, architecture, ADR, docs, and local secret state.

## Capabilities

### New Capabilities
- `project-context-architecture`: Defines root `ARCHITECTURE.md`, the OpenSpec bridge/index, and the required context sources that make current architecture available to every new chat and workflow.

### Modified Capabilities
- `project-constitution`: Changes the canonical constitution filename to `CONSTITUTION.md` and keeps it as root Git-tracked, non-secret Codex-layer project rules.
- `codex-opsx-workflow`: Requires `/opsx:*` workflows to read constitution and architecture context before relevant planning, implementation, verification, and archive actions.
- `intent-driven-schema`: Updates schema instructions so OpenSpec-generated guidance names `CONSTITUTION.md`, `ARCHITECTURE.md`, and in-force ADR context without adding them as OpenSpec artifacts.
- `adr-lifecycle`: Clarifies that `adr/` remains the durable decision history and that `ARCHITECTURE.md` derives the current architecture snapshot from in-force ADRs.
- `context-aware-review`: Ensures design/proposal reviews use `ARCHITECTURE.md`, ADRs, docs, and constitution as review context when material.
- `template-installation`: Updates Greenfield/Brownfield install guidance for `CONSTITUTION.md`, `ARCHITECTURE.md`, `.secrets.example.env`, and local secrets.
- `overlay-update-safety`: Updates preservation rules for root context files, architecture docs, ADRs, and local secret files.

## Impact

- Affected files: root constitution/architecture docs, `.codex/prompts/opsx-*.md`, `.codex/skills/*`, `openspec/schemas/intent-driven/*`, `openspec/specs/*`, `docs/*`, `README.md`, `README.ru.md`, `INSTALL_CODEX.md`, `AGENTS.md`, `scripts/check-overlay`, and `adr/` index/possible new ADR.
- Current `PROJECT_CONSTITUTION.md` content must migrate to `CONSTITUTION.md`; no secret values are copied or introduced.
- OpenSpec CLI remains unchanged and does not read these files itself; the Codex layer is responsible for preflight and context loading.
- Relevant constitution constraints: use OpenSpec CLI 1.3.x-compatible public commands, keep the project-local `intent-driven` schema, keep secrets in `.secrets.local.env`, and run `openspec validate --all --strict`, `openspec schema validate intent-driven`, and `scripts/check-overlay` after implementation.
