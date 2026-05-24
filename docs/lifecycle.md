# Intent-driven lifecycle

## Artifact graph

| Artifact | Path | Depends on | Purpose |
|---|---|---|---|
| proposal | `openspec/changes/<change>/proposal.md` | — | Intent, value, capabilities, impact. |
| specs | `openspec/changes/<change>/specs/**/spec.md` | proposal | Observable behavior and scenarios. |
| design | `openspec/changes/<change>/design.md` | proposal, specs | Technical approach, boundaries, risks. |
| adr | `openspec/changes/<change>/adr.md` | design | Per-change ADR review. |
| tasks | `openspec/changes/<change>/tasks.md` | specs, design, adr | Verifiable implementation checklist. |

## Runtime sequence

| Phase | Codex command | OpenSpec calls | Gate |
|---|---|---|---|
| Explore | `/opsx:explore` | usually none | no implementation |
| New | `/opsx:new` | `openspec new change`, `status`, `instructions proposal` | checkpoint scaffold |
| Continue | `/opsx:continue` | `status`, `instructions <artifact>` | checkpoint artifact |
| Fast propose | `/opsx:propose` | repeated `instructions <artifact>` | checkpoint every artifact |
| Apply | `/opsx:apply` | `status`, `instructions apply`, goal preflight, context read | planning committed; optional Goal hand-off before implementation edits |
| Bulk apply | `/opsx:bulk-apply` | `list`, per-change `status`, per-change `instructions apply`, parent goal preflight | 2+ executable changes; optional parent Goal before worktrees/subagents |
| Verify | `/opsx:verify` | `instructions apply`, tests, validation | no critical issues |
| Archive | `/opsx:archive` | validate/archive/sync | integrated implementation |

## Goal-guidance preflight

Codex Goal guidance is an optional orchestration layer for long or risky apply
runs. It does not replace OpenSpec artifacts, ADRs, verification, or Git
checkpoint approval.

`/opsx:apply <change>` evaluates goal guidance after the change is apply-ready
and context is known, but before implementation file edits. It prints a
copy-paste `/goal` prompt and stops when the change has several pending tasks,
material design/ADR constraints, multiple checkpoint boundaries, external
dependencies, generated assets, migrations, or long-running verification. Small,
local applies may continue directly.

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
