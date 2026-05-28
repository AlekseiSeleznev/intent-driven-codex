---
description: Create a new OpenSpec change and generate planning artifacts up to apply-ready state
argument-hint: change name or description
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-propose` to create a change and generate planning artifacts, including mandatory `grill.md`, `design-review.md`, and `test-plan.md`. For grill gates, run `grill-with-docs` automatically; do not use a plain review flow.

Intent-driven adaptation:
- Target lifecycle is `proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks`.
- Create `grill.md` after specs and before design using `grill-with-docs`. Create `design-review.md` after design using `grill-with-docs`. If no material questions remain, record that conclusion; otherwise integrate accepted grill resolutions before the next gate.
- Use `gherkin-authoring` guidance for specs.
- Use `c4-diagrams` guidance for non-trivial design boundaries.
- Use `architectural-decision-records` guidance for ADR review after `design-review.md`.
- Create `test-plan.md` after ADR using the project-local `tdd` skill to map requirements/scenarios to vertical RED/GREEN/REFACTOR slices before `tasks.md`.

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
