# Intent-driven lifecycle

## Artifact graph

| Artifact | Path | Depends on | Purpose |
|---|---|---|---|
| proposal | `openspec/changes/<change>/proposal.md` | — | Intent, value, capabilities, impact. |
| specs | `openspec/changes/<change>/specs/**/spec.md` | proposal | Observable behavior and scenarios. |
| design | `openspec/changes/<change>/design.md` | proposal, specs | Technical approach, boundaries, risks. |
| review | `openspec/changes/<change>/review.md` | design | Mandatory `grill-with-docs` context review. |
| adr | `openspec/changes/<change>/adr.md` | review | Per-change ADR review informed by review findings. |
| test-plan | `openspec/changes/<change>/test-plan.md` | adr | TDD or verification-first plan before implementation tasks. |
| tasks | `openspec/changes/<change>/tasks.md` | specs, design, review, adr, test-plan | Verifiable implementation checklist. |

`CONSTITUTION.md` and `ARCHITECTURE.md` are intentionally not in this artifact
graph. They are persistent project context for Codex, not OpenSpec change
artifacts, and they are not archived with changes.

## Runtime sequence

| Phase | Codex command | OpenSpec calls | Project context preflight and gate |
|---|---|---|---|
| Explore | `/opsx:explore` | usually none | May run without complete context as diagnostic/exploration, while reporting setup guidance. |
| New | `/opsx:new` | `openspec new change`, `status`, `instructions proposal` | Read constitution first; if missing, only context/setup changes may proceed without override. |
| Continue | `/opsx:continue` | `status`, `instructions <artifact>` | Read constitution and relevant architecture context before artifact work; create exactly one ready artifact. |
| Fast propose | `/opsx:propose` | repeated `instructions <artifact>` | Read project context before planning; create proposal/specs/design/review/ADR/test-plan/tasks with checkpoint boundaries. |
| Apply | `/opsx:apply` | `status`, `instructions apply`, goal preflight, context read | Read constitution, architecture, ADR, review, and test-plan context before goal hand-off or implementation; planning committed; optional Goal before edits. |
| Bulk apply | `/opsx:bulk-apply` | `list`, per-change `status`, per-change `instructions apply`, parent goal preflight | Read project context before worktrees/subagents; require 2+ executable changes. |
| Verify | `/opsx:verify` | `instructions apply`, tests, validation | Verify tasks, requirements, design, review resolutions, ADR compliance, and test-plan evidence. |
| Archive | `/opsx:archive` | validate/archive/sync | Read project context first; do not archive `CONSTITUTION.md`, `ARCHITECTURE.md`, or local secrets. |

## Mandatory review gate

The `review` artifact records the `grill-with-docs` result. Codex reads the
proposal, specs, design, constitution, architecture, ADRs, project docs, and
relevant code before recording review conclusions. If no material questions
remain, `review.md` says so. If material findings change scope, behavior,
approach, durable decisions, or verification expectations, the affected OpenSpec
artifacts must be updated or the finding must be explicitly deferred before ADR.

## Mandatory test-plan gate

The `test-plan` artifact records classic TDD where feasible or the strongest
verification-first substitute when classic unit-test-first flow does not fit the
technology or change type. It maps requirements/scenarios to checks or manual
evidence and defines required checks before apply completion and archive.

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

Secret values are handled outside the lifecycle. The constitution may list
credential variable names, but real values belong only in ignored
`.secrets.local.env` or local environment variables. Codex reads those values
only when the current workflow actually needs a listed external system and must
never print, stage, commit, archive, or copy them.

## Goal-guidance preflight

Codex Goal guidance is an optional orchestration layer for long or risky apply
runs. It does not replace OpenSpec artifacts, review, ADRs, test-plan evidence,
verification, or Git checkpoint approval.

`/opsx:apply <change>` evaluates goal guidance after the change is apply-ready
and context is known, but before implementation file edits. It prints a
copy-paste `/goal` prompt and stops when the change has several pending tasks,
material design/review/ADR/test-plan constraints, multiple checkpoint
boundaries, external dependencies, generated assets, migrations, or long-running
verification. Small, local applies may continue directly, and explicit user
direction can bypass goal handoff for the current run.

## Support skill conditions

| Skill | Use when | Skip when |
|---|---|---|
| `grill-with-docs` | creating or updating `review.md`; material uncertainty, Brownfield context, design/proposal risk | never skip the `review.md` artifact for development/enhancement changes; keep it concise when low risk |
| `gherkin-authoring` | behavior specs/scenarios are being drafted | purely internal config/doc task |
| `c4-diagrams` | system/container boundaries or integrations matter | one local code path |
| `architectural-decision-records` | durable architecture decision exists or ADR review is being written | no long-lived decision, but still create per-change `adr.md` |
