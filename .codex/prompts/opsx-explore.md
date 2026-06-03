---
description: Explore an intent, problem, or design space without implementation
argument-hint: idea, bug, change, or question
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-explore`.

Exploration policy:
- Read relevant code, docs, OpenSpec artifacts, and ADRs when they can answer the question.
- Do not implement application changes.
- Do not create OpenSpec artifacts unless the user asks to capture the exploration.
- Use `grill-with-docs` semantics for material proposal/design uncertainty: read docs/code first, ask one question at a time, and update `CONTEXT.md`/ADRs/artifacts when decisions crystallize.
- Recommend `c4-diagrams` when architecture boundaries or runtime relationships matter.
- Recommend `superpowers:brainstorming` only when divergent exploration would materially help.

Output:
- concise findings;
- risks/unknowns;
- recommended next OpenSpec action, usually `/opsx:new <change>` or `/opsx:propose <change>`.
