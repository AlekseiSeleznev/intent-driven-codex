---
description: Verify implementation against OpenSpec artifacts before archive
argument-hint: optional change name
---

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
