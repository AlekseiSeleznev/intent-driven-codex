# Codex agent instructions for intent-driven OpenSpec projects

## Core workflow

Use OpenSpec as the source of truth. For substantial work, follow:

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks -> apply -> verify -> archive
```

Do not implement before `grill.md`, `design.md`, `design-review.md`, `adr.md`, `test-plan.md`, and `tasks.md` are complete and planning artifacts are committed or explicitly overridden.

## Project context and constitution

Before any `/opsx:*` workflow action or direct OpenSpec lifecycle skill action,
read and respect root `CONSTITUTION.md` when present. Treat it as persistent
Git-tracked project rules, not as an OpenSpec change artifact. OpenSpec CLI does
not read it; Codex is responsible for the preflight.

For terminology-sensitive work, also read root `CONTEXT.md` or `CONTEXT-MAP.md` when present. For architecture-sensitive work, also read root `ARCHITECTURE.md`,
`adr/README.md`, and relevant in-force `adr/*.md`. `ARCHITECTURE.md` is the
current snapshot; durable rationale stays in ADRs.

If `CONSTITUTION.md` is missing, report that and offer to create it from the
template or migrate legacy `PROJECT_CONSTITUTION.md` when present. Continue only
for bootstrap-safe or diagnostic actions such as exploration, project context
setup, or `/opsx:check-overlay`, unless the user gives an explicit one-time
override.

If constitution or architecture rules conflict with the request, OpenSpec
artifacts, intended implementation, external-system access, or verification
rules, stop before file changes or external calls and ask the user how to
resolve the conflict.

Secret values belong only in local `.secrets.local.env` or the local
environment. Git-tracked files may contain variable names and empty placeholders
only. Never print, stage, commit, archive, or copy real logins, passwords,
tokens, private URLs, or other secret values.

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

- `grill-with-docs` for mandatory `grill.md` and `design-review.md`; run it automatically at those gates, one material question at a time, and update `CONTEXT.md`/ADRs/artifacts as decisions crystallize;
- `gherkin-authoring` for OpenSpec scenario quality;
- `c4-diagrams` for non-trivial boundaries/integrations;
- `architectural-decision-records` for per-change ADR review and durable ADR decisions;
- `tdd` for mandatory RED/GREEN/REFACTOR apply slices; do not ask whether to use TDD for behavior-changing work;
- `project-constitution` before `/opsx:*` workflows and substantial project work;
- `openspec-git-discipline` whenever lifecycle state and git history interact.

## Git discipline

Checkpoint after every artifact or coherent apply task group. Always show `git status --short` before mutation. Safe lifecycle checkpoint commits run automatically by default when session git discipline is `auto`; manual mode can still require approval. Merge, push, PR creation, archive, destructive git operations, dirty unrelated work, and hard-gate bypasses still require explicit approval.

Overrides are one-time and must name the bypassed gate, trusted files/state, accepted risk, and the next checkpoint.

## ADRs

Every intent-driven change has `openspec/changes/<change>/grill.md`, `design-review.md`, `adr.md`, and `test-plan.md`. Durable ADRs live in `adr/NNNN-kebab-title.md`. Do not rewrite accepted ADR history; supersede with a new ADR. Update `ARCHITECTURE.md` when a durable decision changes the current architecture snapshot.

## Update safety

Do not patch installed OpenSpec packages. The overlay is project-local. After updates, run:

```bash
scripts/check-overlay
```
