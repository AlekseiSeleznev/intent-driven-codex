---
description: Explore an intent, problem, or design space without implementation
argument-hint: idea, bug, change, or question
---

Use skill `openspec-explore`.

Exploration policy:
- Read relevant code, docs, OpenSpec artifacts, and ADRs when they can answer the question.
- Do not implement application changes.
- Do not create OpenSpec artifacts unless the user asks to capture the exploration.
- Prefer `grill-with-docs` over docs-free questioning when a proposal/design risk needs review.
- Recommend `c4-diagrams` when architecture boundaries or runtime relationships matter.
- Recommend `superpowers:brainstorming` only when divergent exploration would materially help.

Output:
- concise findings;
- risks/unknowns;
- recommended next OpenSpec action, usually `/opsx:new <change>` or `/opsx:propose <change>`.
