---
id: adr-0004
status: accepted
date: 2026-05-28
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/add-mandatory-review-and-tdd-gates
related-adrs:
  - adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
  - adr/0003-formalize-project-context-entrypoints.md
supersedes: []
superseded_by:
  - adr/0005-adopt-matt-grill-and-tdd-gates.md
---

# 0004. Adopt Mandatory Review and Test-Plan Gates

## Status

Accepted.

## Context

ADR 0001 establishes `intent-driven-codex` as a project-local Codex/OpenSpec overlay. OpenSpec remains the lifecycle engine, Codex prompts and skills own workflow behavior, and durable decisions live under top-level `adr/`.

ADR 0003 formalizes project context entry points: root `CONSTITUTION.md`, root `ARCHITECTURE.md`, top-level `adr/`, `openspec/README.md` as a bridge, Codex-layer context loading, and local-only secret boundaries.

The current intent-driven lifecycle is:

```text
proposal -> specs -> design -> adr -> tasks
```

That lifecycle captures intent, behavior, design, ADR review, and implementation tasks, but it leaves two important disciplines as conditional guidance rather than enforceable planning gates:

1. `grill-with-docs` review can happen in chat without a durable artifact that records questions, answers, resolutions, and required artifact updates.
2. TDD or verification-first thinking can happen after tasks are already written, which makes it too easy for implementation plans to omit concrete evidence mapping.

The user wants every new development or enhancement to pass through context-aware review and TDD/verification-first planning, especially for new projects installed from this template.

## Decision

We will expand the intent-driven OpenSpec lifecycle with two mandatory planning artifacts:

```text
proposal -> specs -> design -> review -> adr -> test-plan -> tasks
```

The new artifacts are:

1. `openspec/changes/<change>/review.md`
   - Created after `design.md` and before `adr.md`.
   - Records mandatory `grill-with-docs` review for development and enhancement changes.
   - Captures context reviewed, plan summary, material questions, recommended answers, user answers when needed, accepted resolutions, required artifact updates, and remaining risks.
   - May be concise when no material uncertainty remains, but must not be skipped for development/enhancement changes.

2. `openspec/changes/<change>/test-plan.md`
   - Created after `adr.md` and before `tasks.md`.
   - Records TDD or the strongest feasible verification-first substitute.
   - Maps requirements/scenarios to automated or manual checks.
   - Defines required apply and archive verification evidence.
   - Documents limitations when classic test-first automation is not feasible.

The implementation remains a project-local overlay:

- update `openspec/schemas/intent-driven/schema.yaml` and templates;
- update `.codex/prompts/opsx-*` and `.codex/skills/openspec-*` behavior/guidance;
- update docs, canonical specs, and overlay checks;
- continue using public OpenSpec CLI commands;
- do not patch installed OpenSpec packages;
- keep `CONSTITUTION.md`, `ARCHITECTURE.md`, `adr/`, and `.secrets.local.env` outside OpenSpec change artifacts.

This ADR references ADR 0001 and ADR 0003 but does not supersede them.

## Consequences

### Positive

- Development/enhancement changes get a durable review artifact instead of relying on chat-only review state.
- Review findings are integrated before ADR and task planning, reducing missed Brownfield or architecture conflicts.
- Test strategy is decided before implementation tasks, improving requirement-to-evidence traceability.
- OpenSpec can enforce readiness because `review` and `test-plan` become artifact dependencies.
- New projects installed from the template inherit stronger defaults without custom prompting.
- The overlay remains upgradeable because OpenSpec itself is not patched.

### Negative / Trade-offs

- The lifecycle is breaking for active changes created under the previous schema; they may need explicit `review.md` and `test-plan.md` artifacts before apply.
- Small changes get additional files, though the content can be concise when risk is low.
- More prompts, skills, docs, templates, and smoke checks must stay in sync with the artifact graph.
- Some technology stacks cannot support classic unit-test-first flow, so agents must carefully document verification-first substitutes rather than pretending TDD was performed.

## Alternatives Considered

### Keep review and test planning inside `design.md`

Rejected. Embedded sections would be less visible, less checkpointable, and would not let OpenSpec block ADR or tasks until review and verification planning are complete.

### Keep `grill-with-docs` as optional skill guidance

Rejected. Optional review does not satisfy the user requirement that every development/enhancement pass through context-aware review, especially in new projects.

### Require only classic unit-test TDD

Rejected. The template must support schema, documentation, 1C, integration, and Codex overlay changes where classic unit tests may not be feasible. The durable rule is verification-first planning with classic TDD whenever feasible.

### Patch OpenSpec CLI to enforce the new behavior globally

Rejected. ADR 0001 requires a project-local overlay and public OpenSpec CLI usage. Patching OpenSpec would make updates brittle.

### Auto-migrate all active old-schema changes

Rejected. Automatic migration would guess review and verification evidence after the fact. Active changes should explicitly create `review.md` and `test-plan.md` or stay on the old template version until migrated.

## Implementation References

- `openspec/schemas/intent-driven/schema.yaml`
- `openspec/schemas/intent-driven/templates/review.md`
- `openspec/schemas/intent-driven/templates/test-plan.md`
- `.codex/prompts/opsx-*.md`
- `.codex/skills/openspec-*`
- `.codex/skills/grill-with-docs/SKILL.md`
- `scripts/check-overlay`
- `scripts/install-overlay`
- `docs/lifecycle.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `openspec/changes/add-mandatory-review-and-tdd-gates/`

## Revisit Criteria

Revisit this ADR if:

- OpenSpec introduces native review/test-plan lifecycle support that makes local schema customization unnecessary;
- Codex provides a first-class review or TDD planning mechanism that can replace project-local artifact guidance;
- the expanded lifecycle creates too much overhead for typical changes and a structured lightweight path is needed;
- target project technology stacks require a more formal verification taxonomy than the current test-plan template provides.
