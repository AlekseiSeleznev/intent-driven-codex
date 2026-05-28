---
description: Sync OpenSpec delta specs into canonical specs without archiving
argument-hint: change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-sync-specs`.

Steps:
1. Run `openspec status --change <name> --json`.
2. Inspect `openspec/changes/<name>/specs/**/spec.md`.
3. Merge delta specs into `openspec/specs/<capability>/spec.md` using OpenSpec delta semantics.
4. Run `openspec validate <name> --type change --strict` before and after when applicable.
5. Show changed canonical specs and propose a checkpoint.

Guardrails:
- Do not archive.
- Do not overwrite canonical specs without showing the delta summary.
- Do not commit unless explicitly approved.
