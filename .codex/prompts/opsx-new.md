---
description: Start a new intent-driven OpenSpec change
argument-hint: change name or description
---

Use skill `openspec-new-change` to start a new OpenSpec change.

Default behavior:
- Use the repository default schema from `openspec/config.yaml`.
- In this template that should be `intent-driven`.
- Pass `--schema <name>` only if the user explicitly requests another schema.

Steps:
1. Ensure the working tree is clean or only contains files the user explicitly wants to keep outside this change.
2. Run `openspec new change "<name>"`.
3. Run `openspec status --change "<name>"`.
4. Show instructions for the first ready artifact with `openspec instructions <artifact> --change "<name>"`.
5. Stop before creating the first artifact.
6. Because creating a change is an OpenSpec state change, show `git status --short` and propose a checkpoint before any dependent artifact is created. Do not let a dependent artifact consume the new scaffold until checkpointed or explicitly overridden.

Guardrails:
- Do not create artifacts in `/opsx:new`.
- Do not commit unless the user explicitly approves.
- Do not use external agent-runtime commands or assumptions.
