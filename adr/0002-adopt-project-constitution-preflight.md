---
id: adr-0002
status: accepted
date: 2026-05-25
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/add-project-constitution
related-adrs:
  - adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
supersedes: []
superseded_by:
  - adr/0003-formalize-project-context-entrypoints.md
---

# 0002. Adopt Project Constitution Preflight

## Status

Accepted.

## Context

ADR 0001 establishes `intent-driven-codex` as a project-local Codex/OpenSpec overlay: OpenSpec remains the lifecycle engine, the overlay owns Codex prompts and skills, and installed OpenSpec packages are not patched.

The project now needs a durable project-level rule source that Codex must not miss while creating, applying, verifying, or archiving OpenSpec changes. OpenSpec artifacts capture change-specific intent, but they are archived with changes and are not suitable for persistent project rules such as required technologies, MCP server usage, external-system access, secret handling, documentation sources, verification rules, and additional AI instructions.

This decision extends ADR 0001. It does not supersede ADR 0001: the overlay remains project-local and OpenSpec CLI remains unchanged.

## Decision

We will adopt a project constitution preflight as a Codex-layer extension of the existing overlay.

The overlay will include:

- a root `PROJECT_CONSTITUTION.md` file for Git-tracked, non-secret project rules;
- a local `.secrets.local.env` file for secret values such as logins, passwords, tokens, and service URLs;
- optional tracked secret examples that contain variable names or empty placeholders only;
- a shared `.codex/skills/project-constitution/SKILL.md` preflight used by `/opsx:*` prompts and direct `openspec-*` skill invocations;
- instructional guidance in `openspec/schemas/intent-driven/schema.yaml` so artifact and apply instructions remind Codex to account for the constitution.

The constitution preflight will:

1. read `PROJECT_CONSTITUTION.md` before substantive `/opsx:*` workflow actions;
2. stop on missing constitution except for strictly bounded bootstrap-safe or diagnostic actions;
3. stop when constitution rules conflict with the user request, OpenSpec artifacts, or intended implementation actions;
4. read `.secrets.local.env` only when the current workflow actually needs access to an external system listed in the constitution;
5. report missing credential variable names without revealing values;
6. never print, stage, commit, archive, or otherwise expose real secret values.

The missing-constitution bootstrap exception is intentionally narrow. It applies only to explicit constitution/setup work such as `add-project-constitution`, `initialize-project-constitution`, or `update-project-constitution-template`, and only when the next action creates or edits constitution/bootstrap artifacts, templates, prompts, skills, docs, or safe setup checks. It does not allow unrelated product behavior, external-system access, apply, verify, sync, archive, or bulk workflows.

## Consequences

### Positive

- Codex gets a persistent project rule source that survives OpenSpec archive.
- Project-specific technologies, MCP usage, documentation policy, verification rules, and additional AI instructions become visible before workflow actions.
- Secret values stay out of Git and OpenSpec artifacts while credential variable names remain reviewable.
- OpenSpec CLI remains upgradeable because the enforcement lives in the project-local Codex overlay.
- The strict bootstrap exception avoids deadlock while preventing missing constitution from becoming a general bypass.

### Negative / Trade-offs

- Every `/opsx:*` entry point and direct `openspec-*` skill path must remember to invoke the shared preflight.
- Natural-language constitution rules cannot be mechanically enforced with perfect precision; Codex must use judgment and stop on obvious conflicts.
- Users must maintain `PROJECT_CONSTITUTION.md` as project rules evolve.
- Local `.secrets.local.env` introduces handling risk; the overlay must consistently avoid printing or committing its values.

## Alternatives Considered

### Put constitution enforcement only in `intent-driven` schema YAML

Rejected. Schema instructions can guide artifact creation, but they cannot read files, detect missing secrets, select MCP tools, or stop every `/opsx:*` workflow before action.

### Duplicate constitution rules in every `/opsx:*` prompt

Rejected. Duplicating the full policy across prompts and skills would drift over time. A shared preflight skill keeps the behavior consistent while prompts and skills contain short entry-point reminders.

### Patch OpenSpec CLI to read `PROJECT_CONSTITUTION.md`

Rejected. This violates ADR 0001 and would make OpenSpec upgrades brittle.

### Store external-system credentials in `PROJECT_CONSTITUTION.md`

Rejected. The constitution must be Git-tracked and reviewable, so it must contain only non-secret rules and credential variable names. Real values belong in local `.secrets.local.env`.

### Block all workflows when the constitution is missing

Rejected. Absolute blocking would prevent bootstrap actions needed to create or repair the constitution itself.

## Implementation References

- `PROJECT_CONSTITUTION.md`
- `.secrets.local.env`
- `.secrets.example.env`
- `.codex/skills/project-constitution/SKILL.md`
- `.codex/prompts/opsx-*.md`
- `.codex/skills/openspec-*/SKILL.md`
- `openspec/schemas/intent-driven/schema.yaml`
- `openspec/changes/add-project-constitution/`
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`

## Revisit Criteria

Revisit this ADR if:

- OpenSpec introduces a native, project-level rule/constitution mechanism that can satisfy the same requirements without patching;
- Codex changes prompt or skill execution in a way that provides a more reliable shared preflight mechanism;
- the project adopts a structured machine-validated constitution format instead of Markdown;
- secret handling moves to a managed secret store rather than local `.secrets.local.env`.
