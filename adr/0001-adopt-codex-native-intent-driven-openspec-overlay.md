---
id: adr-0001
status: accepted
date: 2026-05-24
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/implement-intent-driven-codex-template
supersedes: []
superseded_by: []
---

# 0001. Adopt a Codex-native Intent-Driven OpenSpec Overlay

## Status

Accepted.

## Context

This repository builds `intent-driven-codex`: a reusable template that brings Intent-Driven Development to Codex while preserving OpenSpec as the lifecycle and source-of-truth engine.

The project was assembled from these upstream ideas:

- `intent-driven-dev/openspec-schemas` for the intent-driven OpenSpec schema concept;
- `intent-driven-dev/intent-driven-template` for workflow conventions, command/skill structure, ADR/C4/Gherkin guidance, git discipline, and bulk workflow ideas;
- the article about Spec-Driven Development with OpenSpec and OpenCode, adapted here for Codex instead of OpenCode.

The project needs a durable architecture decision before archiving the bootstrap change because the overlay architecture, ownership boundaries, and lifecycle gates are long-lived project commitments, not only implementation details.

## Decision

We will implement Intent-Driven Development for Codex as a **project-local OpenSpec overlay**, not as a fork or patch of OpenSpec.

The overlay consists of:

- project-local OpenSpec schema `openspec/schemas/intent-driven/`;
- Codex prompts under `.codex/prompts/opsx-*.md`;
- Codex skills under `.codex/skills/*`;
- documentation and install guidance under `README.md`, `INSTALL_CODEX.md`, `AGENTS.md`, and `docs/`;
- safe helper scripts such as `scripts/check-overlay` and `scripts/install-overlay`;
- durable ADR history under top-level `adr/`.

The canonical lifecycle is:

```text
proposal -> specs -> design -> adr -> tasks -> apply -> verify -> archive
```

The Codex adaptation makes these durable choices:

1. OpenSpec remains the engine and is accessed only through public CLI commands.
2. The default schema is project-local `intent-driven` with artifact order `proposal -> specs -> design -> adr -> tasks`.
3. `design` depends on both `proposal` and `specs` so technical design follows agreed behavior.
4. Every intent-driven change has a per-change ADR review at `openspec/changes/<change>/adr.md`.
5. Durable architectural decisions live as append-only files in top-level `adr/NNNN-*.md`.
6. Accepted ADRs are not rewritten; changed decisions are represented by superseding ADRs.
7. Proposal/design review uses `grill-with-docs` rather than a docs-free review flow.
8. Specs stay OpenSpec Markdown with Gherkin-style scenarios embedded in requirement blocks.
9. Non-trivial design boundaries use C4-style diagrams or an explicit no-diagram rationale.
10. Git checkpoints are mandatory after lifecycle artifacts and coherent apply groups, while mutating git operations still require explicit user approval.
11. OpenSpec updates are handled by validating the overlay and smoke-checking it, not by editing installed OpenSpec package files.
12. Greenfield and Brownfield installation must preserve user-owned artifacts and avoid overwriting active changes, canonical specs, ADR history, source code, or tests.

## Consequences

### Positive

- OpenSpec can be updated normally because this project does not patch OpenSpec internals.
- Codex receives a first-class workflow through `.codex/prompts` and `.codex/skills`.
- The intent-driven lifecycle is explicit, repeatable, and validated by OpenSpec.
- `grill-with-docs` makes review context-aware for Brownfield projects.
- ADR history remains durable and auditable across archived changes.
- Mandatory checkpoints make planning, implementation, verification, and archive states reviewable and recoverable.
- Overlay compatibility can be checked with `scripts/check-overlay` after OpenSpec or template updates.

### Negative / Trade-offs

- The overlay has its own prompts, skills, scripts, and schema files that must be maintained as OpenSpec/Codex evolve.
- Mandatory checkpoints add ceremony for very small changes.
- The dual ADR model requires agents to read both per-change `adr.md` and top-level `adr/*.md`.
- The Codex adaptation intentionally differs from upstream where necessary, for example by making `design` depend on `specs` and by using per-change `adr.md` as the OpenSpec-tracked ADR artifact.

## Alternatives Considered

### Patch or fork OpenSpec

Rejected. It would make OpenSpec upgrades brittle and would mix template behavior with upstream engine behavior.

### Use only Codex prompts without a project-local schema

Rejected. Prompt-only workflow would not let OpenSpec manage artifact dependencies, apply readiness, task tracking, and archive semantics for the intent-driven lifecycle.

### Reuse the upstream OpenCode template as-is

Rejected. The target runtime is Codex, not OpenCode. The template must use `.codex/` prompts and skills and avoid `.opencode` assumptions.

### Keep only per-change ADRs and no durable ADR directory

Rejected. Per-change ADR review is useful for gatekeeping, but durable decisions need a stable top-level history that survives archive and can be read by future changes.

## Implementation References

- `openspec/schemas/intent-driven/schema.yaml`
- `.codex/prompts/opsx-*.md`
- `.codex/skills/*`
- `scripts/check-overlay`
- `scripts/install-overlay`
- `README.md`
- `docs/lifecycle.md`
- `docs/update-safety.md`
- `openspec/changes/implement-intent-driven-codex-template/`

## Revisit Criteria

Revisit this ADR if:

- OpenSpec introduces a native intent-driven lifecycle that makes the local schema unnecessary;
- Codex changes prompt/skill loading in a way that invalidates the `.codex/` overlay structure;
- the project decides to support multiple schemas rather than intent-driven only;
- git checkpoint discipline becomes automated by a trusted project-level policy rather than explicit user approvals.
