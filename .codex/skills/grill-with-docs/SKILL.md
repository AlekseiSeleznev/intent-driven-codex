---
name: grill-with-docs
description: Use when a proposal or design needs rigorous context-aware review against OpenSpec artifacts, ADRs, project documents, and relevant code before implementation.
license: MIT
compatibility: Codex lazy-loaded skill for OpenSpec projects.
---

# Grill With Docs

Use this optional skill only after recommendation and user consent.

Stress-test a proposal, design, or plan against available documents and code.
Ask focused questions one at a time, and provide your recommended answer for
each question.

## Context First

Before asking questions, read relevant context:

- `openspec/changes/<change>/proposal.md`
- `openspec/changes/<change>/design.md`
- `openspec/changes/<change>/tasks.md`
- `openspec/changes/<change>/specs/*/spec.md`
- ADRs and architecture documents when present
- README/AGENTS/install docs that constrain the change
- relevant code paths when the question can be answered by inspection

## Workflow

1. Summarize the plan in 3-5 factual bullets.
2. Identify the highest-risk uncertainty.
3. Ask one question.
4. Include your recommended answer and why.
5. After the user answers, resolve that branch and move to the next highest
   risk.
6. Stop when no material uncertainty remains or the user asks to stop.

## Guardrails

- Do not interrogate low-risk changes for ceremony.
- Do not ask questions that can be answered by reading files.
- Do not invent project constraints.
- If review reveals artifact changes, propose exact artifact updates.
