---
description: Continue an OpenSpec change by creating exactly one ready artifact
argument-hint: optional change name
---

Use skill `openspec-continue-change` to create the next ready artifact.

Intent-driven policy:
- Create exactly one artifact per invocation.
- Follow OpenSpec status and instructions.
- If multiple artifacts are ready, prefer the schema order from `openspec status --change <name> --json` and the intent-driven lifecycle: `proposal -> specs -> design -> adr -> tasks`.
- Read dependency files before creating the artifact.


Artifact support skills:
- For `specs`, recommend `gherkin-authoring` and preserve OpenSpec Markdown as the source of truth.
- For `design`, recommend `c4-diagrams` when boundaries/integrations are non-trivial; otherwise record an explicit no-diagram rationale.
- For `proposal` or `design` review, recommend `grill-with-docs` when material assumptions or Brownfield context exist; do not use the plain review skill.
- For `adr`, recommend `architectural-decision-records` and preserve append-only durable ADR supersession rules.

Git discipline:
- Before writing, identify the next artifact and verify dependency artifacts are already committed or explicitly covered by the user override.
1. Before creating a dependent artifact, run `git status --short`.
2. If prior artifact changes are uncommitted, stop and propose a checkpoint unless the user explicitly overrides.
3. After writing the artifact, run `openspec status --change <name>` and `git status --short`.
4. Propose a checkpoint and stop.

Guardrails:
- Do not skip artifacts.
- Do not create more than one artifact.
- Do not commit unless the user explicitly approves.
