---
description: Create a new OpenSpec change and generate planning artifacts up to apply-ready state
argument-hint: change name or description
---

Use skill `openspec-propose` to create a change and generate planning artifacts. For review, use `grill-with-docs`; do not use the plain review skill.

Intent-driven adaptation:
- Target lifecycle is `proposal -> specs -> design -> adr -> tasks`.
- Recommend `grill-with-docs` after proposal/design drafts when scope, risk, assumptions, integration impact, or Brownfield context are material. It replaces the plain review skill in this template.
- Use `gherkin-authoring` guidance for specs.
- Use `c4-diagrams` guidance for non-trivial design boundaries.
- Use `architectural-decision-records` guidance for ADR review.

Git discipline:
- Before starting, run `git branch --show-current` and `git status --short`; warn if not on `main` or if unrelated changes are present.
- After each artifact is written, run `openspec status --change <name> --json` and `git status --short`; show changed files and request approval for the checkpoint before writing a dependent artifact.
- The normal workflow requires a checkpoint after each artifact.
- If the user has not pre-approved batch checkpoints, stop after each artifact and ask for commit approval.
- If the user explicitly pre-approves a batch workflow, still show each checkpoint and final diff summary.

Guardrails:
- Do not implement code.
- Do not commit/merge/push unless explicitly approved.
- Do not use external agent-runtime assumptions.
