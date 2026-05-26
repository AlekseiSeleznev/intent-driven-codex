---
description: Verify implementation against OpenSpec artifacts before archive
argument-hint: optional change name
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-verify-change`.

Verification context:
- Run `openspec instructions apply --change <name> --json` and read all `contextFiles`.
- Read top-level `adr/*.md` when present and derive the in-force ADR set.
- Check tasks, requirements, scenarios, design decisions, and ADR constraints.

Output:
- Completeness: task completion and requirement coverage.
- Correctness: implementation evidence for requirements/scenarios.
- Coherence: design and ADR adherence.
- Critical/warning/suggestion issues with concrete file references.

Guardrails:
- Verification does not replace merge or archive gates.
- Do not mark ready for archive if critical issues exist.
