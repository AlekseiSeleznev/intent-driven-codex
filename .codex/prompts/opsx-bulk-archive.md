---
description: Archive multiple completed OpenSpec changes with conflict checks
argument-hint: optional change names
---

Use skill `openspec-bulk-archive-change`.

Rules:
- List active changes with `openspec list --json`.
- Let the user choose the changes unless a clear explicit list was provided.
- Validate artifact/task completion for each change.
- Detect multiple selected changes touching the same capability spec.
- Resolve spec conflicts by inspecting delta specs and implementation evidence.
- Ask for explicit confirmation before archiving.

Guardrails:
- Do not archive incomplete or conflicting changes silently.
- Do not commit archive/spec sync changes unless explicitly approved.
- Do not use external agent-runtime assumptions.
