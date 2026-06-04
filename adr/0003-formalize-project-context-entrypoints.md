---
id: adr-0003
status: accepted
date: 2026-05-26
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/formalize-project-context-architecture
related-adrs:
  - adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
  - adr/0002-adopt-project-constitution-preflight.md
supersedes:
  - adr/0002-adopt-project-constitution-preflight.md
superseded_by: []
---

# 0003. Formalize Project Context Entry Points

## Status

Accepted.

## Context

ADR 0001 establishes `intent-driven-codex` as a project-local Codex/OpenSpec overlay: OpenSpec remains the lifecycle engine, Codex prompts and skills own workflow behavior, and durable decisions live under top-level `adr/`.

ADR 0002 introduced a Codex-layer project constitution preflight using root `PROJECT_CONSTITUTION.md` for persistent non-secret project rules and `.secrets.local.env` for local secret values. That decision solved the missing persistent-rule source, but it did not provide a compact current architecture entry point for new chats, and the canonical constitution filename is more verbose than desired.

The project now needs a clearer always-on context model:

- project rules must be easy to find and read before `/opsx:*` work;
- current architecture must be visible at the start of a new chat without reconstructing it from archived changes;
- durable ADRs must remain the decision history rather than becoming a mutable snapshot;
- OpenSpec must remain the artifact/lifecycle engine, not the owner of local secrets or all persistent AI policy.

This decision references ADR 0001 and does not supersede it. It supersedes ADR 0002 because the canonical constitution filename and broader context entrypoint model replace the earlier `PROJECT_CONSTITUTION.md`-only convention while preserving the Codex-layer preflight and local-secret boundary.

## Decision

We will formalize the following project context entry points:

1. Root `CONSTITUTION.md` is the canonical Git-tracked, non-secret project rule file for Codex workflows.
   - Legacy `PROJECT_CONSTITUTION.md` may be used only as migration input when `CONSTITUTION.md` is absent.
   - Codex must not keep both files as competing active rule sources.

2. Root `ARCHITECTURE.md` is the canonical current architecture snapshot.
   - It summarizes the current architecture and links to in-force durable ADRs.
   - It is not a replacement for ADR history.
   - When an accepted durable ADR changes current architecture, the relevant tasks must update `ARCHITECTURE.md`.

3. Top-level `adr/` remains the append-only durable decision history.
   - Accepted ADRs are not rewritten to change decisions.
   - New decisions supersede older decisions through new ADR files and the ADR index.

4. `openspec/README.md` is a bridge/index, not a policy or secret store.
   - It explains that `CONSTITUTION.md`, `ARCHITECTURE.md`, `adr/`, and relevant `docs/` are persistent project context outside the OpenSpec change artifact graph.
   - It also explains that `.secrets.local.env` is local-only and not an OpenSpec artifact.

5. The Codex layer remains responsible for reading project context.
   - `/opsx:*` prompts and direct `openspec-*` skills read `CONSTITUTION.md` before workflow actions.
   - Architecture-sensitive planning, design, implementation, verification, and archive work read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force ADR files.
   - OpenSpec CLI remains unchanged and does not read these files itself.

6. Secret values remain local-only.
   - Git-tracked files may contain variable names, non-secret labels, and empty placeholders only.
   - Real values stay in `.secrets.local.env` or the local environment and are read only when a workflow needs the listed external system.

## Consequences

### Positive

- New chats get stable, short entry points for project rules and current architecture.
- The constitution filename is easier to remember and applies consistently across projects.
- ADRs remain durable history while `ARCHITECTURE.md` provides a quick current-state map.
- OpenSpec stays upgradeable because the CLI is not patched and persistent context remains a Codex-layer concern.
- Secrets stay outside Git and outside the OpenSpec artifact/archive flow.

### Negative / Trade-offs

- The project must update all prompts, skills, schema guidance, docs, specs, and checks that still reference `PROJECT_CONSTITUTION.md`.
- `ARCHITECTURE.md` can drift unless ADR reviews and tasks explicitly update it when architecture changes.
- Codex still relies on prompt/skill discipline to run preflight consistently because OpenSpec CLI does not enforce the context reads.
- Existing projects using `PROJECT_CONSTITUTION.md` need migration guidance.

## Alternatives Considered

### Keep `PROJECT_CONSTITUTION.md` as canonical

Rejected. The user prefers the shorter `CONSTITUTION.md`, and canonical file names should be easy to remember across projects.

### Put constitution and architecture under `openspec/`

Rejected. These files are persistent project context, not OpenSpec change artifacts. Keeping them under root avoids archive confusion and keeps them visible to new chats.

### Use only ADRs for current architecture

Rejected. ADRs are authoritative history, but new chats need a compact current-state snapshot. `ARCHITECTURE.md` provides that snapshot and links back to ADRs for rationale.

### Patch OpenSpec CLI to read the context files

Rejected. ADR 0001 requires a project-local overlay that uses OpenSpec public commands without patching or forking the CLI.

### Store external credentials in the constitution or OpenSpec files

Rejected. Git-tracked files must remain non-secret. Real values belong only in local `.secrets.local.env` or the local environment.

## Implementation References

- `CONSTITUTION.md`
- `ARCHITECTURE.md`
- `openspec/README.md`
- `adr/README.md`
- `.secrets.local.env`
- `.secrets.example.env`
- `.codex/prompts/opsx-*.md`
- `.codex/skills/*`
- `openspec/schemas/intent-driven/schema.yaml`
- `openspec/changes/formalize-project-context-architecture/`
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`
- `adr/0002-adopt-project-constitution-preflight.md`

## Revisit Criteria

Revisit this ADR if:

- OpenSpec introduces a native project context mechanism that satisfies these requirements without patching;
- Codex provides a stronger first-class preflight/context mechanism that can replace prompt/skill reminders;
- the project adopts a structured machine-validated constitution or architecture manifest instead of Markdown;
- secret handling moves from local `.secrets.local.env` to a managed local or remote secret store with safe Codex integration.
