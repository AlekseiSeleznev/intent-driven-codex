# Intent-driven lifecycle

## Artifact graph

| Artifact | Path | Depends on | Purpose |
|---|---|---|---|
| proposal | `openspec/changes/<change>/proposal.md` | — | Intent, value, capabilities, impact. |
| specs | `openspec/changes/<change>/specs/**/spec.md` | proposal | Observable behavior and scenarios. |
| design | `openspec/changes/<change>/design.md` | proposal, specs | Technical approach, boundaries, risks. |
| adr | `openspec/changes/<change>/adr.md` | design | Per-change ADR review. |
| tasks | `openspec/changes/<change>/tasks.md` | specs, design, adr | Verifiable implementation checklist. |

`CONSTITUTION.md` and `ARCHITECTURE.md` are intentionally not in this artifact
graph. They are persistent project context for Codex, not OpenSpec change
artifacts, and they are not archived with changes.

## Runtime sequence

| Phase | Codex command | OpenSpec calls | Project context preflight and gate |
|---|---|---|---|
| Explore | `/opsx:explore` | usually none | May run without complete context as diagnostic/exploration, while reporting setup guidance. |
| New | `/opsx:new` | `openspec new change`, `status`, `instructions proposal` | Read constitution first; if missing, only context/setup changes may proceed without override. |
| Continue | `/opsx:continue` | `status`, `instructions <artifact>` | Read constitution and relevant architecture context before artifact work; stop on conflicts before writing files. |
| Fast propose | `/opsx:propose` | repeated `instructions <artifact>` | Read project context before planning; checkpoint every artifact. |
| Apply | `/opsx:apply` | `status`, `instructions apply`, goal preflight, context read | Read constitution, architecture, and ADR context before goal hand-off or implementation; planning committed; optional Goal before edits. |
| Bulk apply | `/opsx:bulk-apply` | `list`, per-change `status`, per-change `instructions apply`, parent goal preflight | Read project context before worktrees/subagents; require 2+ executable changes. |
| Verify | `/opsx:verify` | `instructions apply`, tests, validation | Read constitution verification rules and architecture/ADR constraints; stop on missing credentials or conflicts. |
| Archive | `/opsx:archive` | validate/archive/sync | Read project context first; do not archive `CONSTITUTION.md`, `ARCHITECTURE.md`, or local secrets. |

## Project context preflight

Codex runs the project context preflight before `/opsx:*` workflow actions and
direct OpenSpec lifecycle skill actions. The preflight reads `CONSTITUTION.md`
when present and applies relevant project rules for required technologies, MCP
servers, external systems, secret handling, documentation sources, verification
rules, and additional AI instructions.

For architecture-sensitive work, Codex also reads `ARCHITECTURE.md`,
`adr/README.md`, and relevant in-force `adr/*.md` files. The architecture file is
a current snapshot; ADRs remain the durable rationale.

Missing context behavior is strict but not deadlocking:

- exploration/read-only diagnostics may continue;
- creating or repairing the constitution or architecture snapshot may continue;
- `/opsx:check-overlay` may continue and report missing files as setup guidance;
- dependent planning, apply, verify, sync, archive, and bulk workflows stop
  unless the user gives an explicit one-time override.

If `CONSTITUTION.md` is missing but legacy `PROJECT_CONSTITUTION.md` exists,
Codex treats the legacy file only as migration input and does not maintain both
files as competing active rule sources.

Conflict behavior is also strict. If constitution or architecture rules
contradict the user request, OpenSpec artifacts, an intended implementation
action, external-system access, or required verification, Codex stops before
modifying files or calling external systems and asks the user to resolve the
conflict.

Secret values are handled outside the lifecycle. The constitution may list
credential variable names, but real values belong only in ignored
`.secrets.local.env` or local environment variables. Codex reads those values
only when the current workflow actually needs a listed external system and must
never print, stage, commit, archive, or copy them.

## Goal-guidance preflight

Codex Goal guidance is an optional orchestration layer for long or risky apply
runs. It does not replace OpenSpec artifacts, ADRs, verification, or Git
checkpoint approval.

`/opsx:apply <change>` evaluates goal guidance after the change is apply-ready
and context is known, but before implementation file edits. It prints a
copy-paste `/goal` prompt and stops when the change has several pending tasks,
material design/ADR constraints, multiple checkpoint boundaries, external
dependencies, generated assets, migrations, or long-running verification. Small,
local applies may continue directly, and explicit user direction can bypass goal
handoff for the current run.

`/opsx:bulk-apply <changes...>` evaluates parent goal guidance after executable
changes are known, but before worktree creation or subagent dispatch. For two or
more executable changes it prints one parent `/goal` prompt unless the run is
already inside a covering active goal or the user explicitly requested no goal.

Generated goals include the literal `/opsx:apply <change>` or
`/opsx:bulk-apply <changes...>` workflow, fallback to
`openspec-apply-change` or `openspec-bulk-apply-change`, verify-gated completion
criteria, final reporting requirements, and stop-without-completion conditions.
They must stop on dirty planning artifacts, blocked OpenSpec state, missing
credentials/secrets, unavailable external services, external check failures,
artifact contradictions, required design/spec/ADR decisions, worktree/subagent
failures, merge/worktree conflicts, or any archive, merge, push, destructive
operation, staging, or commit that lacks separate explicit approval.

## Optional skill conditions

| Skill | Use when | Skip when |
|---|---|---|
| `grill-with-docs` | material uncertainty, Brownfield context, design/proposal risk | small obvious change |
| `gherkin-authoring` | behavior specs/scenarios are being drafted | purely internal config/doc task |
| `c4-diagrams` | system/container boundaries or integrations matter | one local code path |
| `architectural-decision-records` | durable architecture decision exists | no long-lived decision |
