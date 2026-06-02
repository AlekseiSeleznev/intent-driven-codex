---
id: adr-0005
status: accepted
date: 2026-05-28
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/harden-mandatory-grill-and-tdd-gates
related-adrs:
  - adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
  - adr/0003-formalize-project-context-entrypoints.md
  - adr/0004-adopt-mandatory-review-and-test-plan-gates.md
supersedes:
  - adr/0004-adopt-mandatory-review-and-test-plan-gates.md
superseded_by: []
---

# 0005. Adopt Matt Grill and TDD Gates

## Status

Accepted.

## Context

ADR 0001 establishes `intent-driven-codex` as a project-local Codex/OpenSpec
overlay. ADR 0003 establishes the root project context model. ADR 0004 expanded
the lifecycle with `review.md` and `test-plan.md`, but that v0.1.2 structure was
still too soft: `grill-with-docs` could be treated as a review recommendation,
and TDD could degrade into after-the-fact verification.

The project now needs the full Matt Pocock `grill-with-docs` and `tdd` skills to
be embedded as operational gates for all new projects installed from this
template.

## Decision

We will harden the intent-driven lifecycle to:

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks
```

The new durable rules are:

1. `grill.md` is a mandatory pre-design Matt `grill-with-docs` gate.
2. `design-review.md` is a mandatory post-design Matt `grill-with-docs` gate.
3. Codex runs `grill-with-docs` automatically at both gates; it does not ask
   whether to run it.
4. Grill asks one material question at a time only when repository context cannot
   answer the uncertainty.
5. Resolved project language belongs in `CONTEXT.md` or context-local glossary
   files selected through `CONTEXT-MAP.md`.
6. `CONTEXT.md` is glossary-only and does not replace `CONSTITUTION.md`,
   `ARCHITECTURE.md`, or ADRs.
7. The project-local Matt `tdd` skill is the canonical implementation discipline
   for `/opsx:apply` behavior changes.
8. `/opsx:apply` follows vertical RED -> GREEN -> REFACTOR slices and does not
   write production code for a behavior before RED evidence unless `test-plan.md`
   records an explicit approved exception.
9. `/opsx:verify` treats missing RED/GREEN evidence as critical for
   behavior-changing tasks unless an exception was approved before apply.

This ADR supersedes ADR 0004 for lifecycle gate structure and TDD enforcement.
ADR 0001 and ADR 0003 remain in force.

## Consequences

### Positive

- New changes cannot reach design before key domain/context uncertainty is
  resolved.
- Technical designs are stress-tested before ADR/test planning/tasks consume
  them.
- New projects receive a project-local TDD discipline without depending on an
  optional external plugin.
- `CONTEXT.md` gives Codex a stable glossary entry point that complements, rather
  than competes with, constitution and architecture files.
- Apply and verify have concrete RED/GREEN evidence expectations.

### Negative / Trade-offs

- The lifecycle is more demanding and has one additional planning gate compared
  with v0.1.2.
- Existing active changes created with v0.1.2 must be migrated if they should use
  the v0.1.3 schema.
- Documentation and scripts must distinguish glossary, policy, architecture, and
  ADR responsibilities carefully.

## Alternatives Considered

### Keep `review.md` as the only grill artifact

Rejected. A single post-design review still allows design to be created before
terminology and scope uncertainty are resolved.

### Depend on Superpowers TDD

Rejected as the canonical rule because it is plugin-provided and may not be
installed in every target project. It can still supplement the vendored Matt TDD
skill when available.

### Put glossary rules into `CONSTITUTION.md`

Rejected. The constitution is policy/rules; Matt `grill-with-docs` expects a
separate glossary file that stays free of implementation details and decisions.
