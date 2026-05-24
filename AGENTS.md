# Codex agent instructions for intent-driven OpenSpec projects

## Core workflow

Use OpenSpec as the source of truth. For substantial work, follow:

```text
proposal -> specs -> design -> adr -> tasks -> apply -> verify -> archive
```

Do not implement before `tasks.md` is complete and planning artifacts are committed or explicitly overridden.

## Codex commands

Prefer the installed `/opsx:*` prompts:

- `/opsx:explore` for investigation without implementation;
- `/opsx:new` to create a change scaffold;
- `/opsx:continue` to create exactly one next artifact;
- `/opsx:propose` only when the user wants a fast path;
- `/opsx:apply` to implement tasks;
- `/opsx:verify` before archive;
- `/opsx:archive` only after verification and integration;
- `/opsx:check-overlay` after OpenSpec/template updates.

## Codex Goal guidance

Codex Goal is optional orchestration for long or risky implementation runs. It
does not replace OpenSpec artifacts, ADRs, tasks, verification, or Git
checkpoint gates.

`/opsx:apply` may stop after apply eligibility is known and print a copy-paste
`/goal ...` prompt when the change has several pending tasks, material
design/ADR constraints, multiple checkpoint boundaries, external dependencies,
or similarly long-running risk. `/opsx:bulk-apply` may print a parent `/goal`
prompt for eligible multi-change runs before creating worktrees or dispatching
subagents. Copy and send the generated prompt as the next message only when you
want Codex Goal to manage that run.

Skip goal generation when the current run is already inside an active Codex Goal
for the same change(s), when the user explicitly asks to continue without a
goal, or when a single apply is small and local. Stop without marking a goal
complete on dirty planning artifacts, blocked OpenSpec state, missing
credentials, unavailable external services, artifact contradictions, required
design/spec/ADR decisions, worktree/subagent failures, or any merge, archive,
push, destructive operation, staging, or commit that lacks explicit approval.

## Support skills

Use these when their conditions are met:

- `grill-with-docs` for proposal/design review with material risk or Brownfield context;
- `gherkin-authoring` for OpenSpec scenario quality;
- `c4-diagrams` for non-trivial boundaries/integrations;
- `architectural-decision-records` for durable ADR decisions;
- `openspec-git-discipline` whenever lifecycle state and git history interact.

## Git discipline

Checkpoint after every artifact or coherent apply task group. Always show `git status --short` before mutation. Do not stage, commit, merge, push, or archive without explicit user approval.

Overrides are one-time and must name the bypassed gate, trusted files/state, accepted risk, and the next checkpoint.

## ADRs

Every intent-driven change has `openspec/changes/<change>/adr.md`. Durable ADRs live in `adr/NNNN-kebab-title.md`. Do not rewrite accepted ADR history; supersede with a new ADR.

## Update safety

Do not patch installed OpenSpec packages. The overlay is project-local. After updates, run:

```bash
scripts/check-overlay
```
