---
description: Sync OpenSpec delta specs into canonical specs without archiving
argument-hint: change name
---

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
