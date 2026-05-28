---
description: Fast-forward OpenSpec planning artifacts with explicit checkpoint policy
argument-hint: change name or description
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Fast-forward is a convenience wrapper around creating all planning artifacts needed before apply.

Use only when the user explicitly wants fast-forward behavior.

Rules:
- Use the repository default schema unless the user requests a schema.
- For `intent-driven`, generate artifacts in order: `proposal`, `specs`, `design`, `review`, `adr`, `test-plan`, `tasks`.
- Follow `openspec instructions <artifact> --change <name> --json` for every artifact.
- Read dependencies before writing each artifact.
- Apply `grill-with-docs` for the mandatory review artifact, `gherkin-authoring`, `c4-diagrams`, `architectural-decision-records`, and verification-first planning guidance when their conditions are met.

Git discipline:
- Before the first artifact and after every artifact, run `git status --short` and show the checkpoint boundary.
- A pre-approved batch may continue through artifacts, but each artifact checkpoint must still be visible in the final summary.
- Mandatory checkpoints still apply.
- If the user has not pre-approved batch checkpoints, stop after the first artifact and recommend `/opsx:continue`.
- Never hide commits or merges.
