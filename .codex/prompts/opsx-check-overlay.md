---
description: Check intent-driven Codex overlay compatibility with OpenSpec
argument-hint: optional --cleanup
---

Project constitution preflight:
- Before this workflow takes action, use skill `project-constitution` to read and enforce root `CONSTITUTION.md`. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`.
- If the constitution is missing, follow the bootstrap-safe/diagnostic rules from that skill; otherwise stop unless the user gives an explicit one-time override.
- Do not read `.secrets.local.env` unless this workflow actually needs a listed external system, and never reveal secret values.

Use skill `openspec-check-overlay`.

Run the repository checker when available:

```bash
scripts/check-overlay
```

The checker runs compatibility checks without patching OpenSpec:

```bash
openspec --version
openspec schemas --json
openspec schema validate intent-driven
openspec list --json
```

Then it creates a uniquely named temporary smoke change, completes minimal proposal/specs/design/review/adr/test-plan/tasks artifacts, verifies `openspec status --change <smoke> --json`, verifies `openspec instructions apply --change <smoke> --json` includes `review` and `test-plan` context, and safely removes only that smoke change unless `--keep-smoke` is passed.

Important:
- Do not run bare `openspec status --json`; OpenSpec 1.3.x requires `--change`.
- Report failures by layer: CLI, local schema, Codex prompt/skill adapter, generated skill, or project artifact.
- Default cleanup is safe and automatic only for a `zz-smoke-intent-overlay-*` change created by this command in the same run. Use `scripts/check-overlay --keep-smoke` to inspect it manually.
