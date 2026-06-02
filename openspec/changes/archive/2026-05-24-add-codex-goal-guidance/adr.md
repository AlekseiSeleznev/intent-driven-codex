## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — status: accepted; remains in force. This change stays within the project-local Codex/OpenSpec overlay, does not patch OpenSpec, keeps OpenSpec artifacts as the lifecycle source of truth, and preserves explicit Git checkpoint discipline.

## Decisions Evaluated

- Use generated copy-paste `/goal` prompts instead of silently creating or starting Codex Goals from `/opsx:apply` or `/opsx:bulk-apply`.
  - Outcome: accepted for this change.
  - Rationale: the user must remain in control of starting goal execution, and the generated next message can embed the exact `/opsx:*` workflow, completion criteria, and stop conditions.
- Treat Codex Goal as an orchestration layer only, not as persistent project state.
  - Outcome: accepted for this change.
  - Rationale: OpenSpec artifacts, canonical specs, durable ADRs, and Git history remain the auditable source of truth.
- Include semantic fallback skill names in generated `/goal` prompts.
  - Outcome: accepted for this change.
  - Rationale: nested slash commands inside goal text may not execute literally in every Codex runtime, so the goal must still be executable as a natural-language instruction using `openspec-apply-change` or `openspec-bulk-apply-change`.
- Generate a parent goal prompt before `/opsx:bulk-apply` creates worktrees or dispatches subagents.
  - Outcome: accepted for this change.
  - Rationale: bulk apply has multiple independent execution streams and should start with a single explicit parent objective before side effects begin.
- Create a new durable top-level ADR for this change.
  - Outcome: rejected.
  - Rationale: the decision refines prompt/skill workflow behavior inside the architectural boundary already accepted by ADR-0001. It does not introduce a new persistent state store, runtime boundary, OpenSpec ownership model, or replacement for the project-local overlay architecture.

## New Durable ADRs Created

- None.

## Superseded ADRs

- None.

## No ADR Needed

- No new durable ADR is needed because ADR-0001 already governs the relevant long-lived architecture: Intent-Driven Codex is a project-local OpenSpec overlay, OpenSpec remains the source-of-truth engine, accepted ADR history is append-only, and mutating operations require explicit user approval.
- This change updates workflow behavior and user guidance within that existing architecture. Its durable behavior will be preserved by the OpenSpec change artifacts, canonical specs after archive, README/agent documentation, and updated Codex prompts/skills.
- Revisit durable ADR creation if a later change auto-creates Codex Goals, persists goal state in project files, changes OpenSpec ownership boundaries, or removes the explicit user hand-off before goal execution.
