---
description: Start a new intent-driven OpenSpec change
argument-hint: change name or description
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.


Artifact review session intent:
- Before key entry workflows create or consume reviewable artifacts, run `scripts/openspec-session-state --status` when the helper exists.
- If `artifactReview.decision` is `unset`, ask whether to enable OpenAI-compatible artifact review for this session; record the answer with `scripts/openspec-session-state --review on` or `--review off`.
- Reuse the recorded session decision on later commands. Use `/opsx:review-status`, `/opsx:review-on`, `/opsx:review-off`, `/opsx:review-reset`, and `/opsx:review-set` to inspect or change it.
- Session settings are non-secret local overlays over `.codex/openspec-artifact-review.json`; do not edit global defaults unless the user explicitly asks.

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
6. Because creating a change is an OpenSpec state change, show `git status --short` and checkpoint the scaffold before any dependent artifact is created. In session git `auto` mode this safe scoped checkpoint may be committed automatically after showing paths/message/hash; manual mode stops for approval.

Guardrails:
- Do not create artifacts in `/opsx:new`.
- Do not merge, push, archive, run destructive git operations, or bypass hard gates unless the user explicitly approves.
- Do not use external agent-runtime commands or assumptions.
