---
description: Archive a completed OpenSpec change after verification and integration gates
argument-hint: optional change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-archive-change`.

Hard gates:
0. Run `git rev-parse --show-toplevel`; archive only from the intended repository.
1. Run `git branch --show-current` and `git status --short`.
2. Confirm implementation is committed and integrated into `main` or the explicitly chosen base branch; if not, stop and explain that verify does not replace merge/integration.
3. Run `/opsx:verify <change>` or confirm a fresh verification report exists with no critical issues.
4. Run `openspec validate <change> --type change --strict`.
5. Assess delta spec sync state before archive.

Archive behavior:
- Prefer `openspec archive <change>` or the archive skill's documented move/sync process.
- Archive only after explicit user approval.
- After archive, show spec/archive changes and propose a checkpoint.

Guardrails:
- A dirty tree, non-integrated implementation, or missing verification can only be bypassed by an explicit user override that names the gate being bypassed; record the override in the archive summary.
- Do not archive from a dirty working tree.
- Do not archive before merge/integration.
- Do not commit unless explicitly approved.
