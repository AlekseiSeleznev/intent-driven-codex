---
description: Check intent-driven Codex overlay compatibility with OpenSpec
argument-hint: optional --cleanup
---

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

Then it creates a uniquely named temporary smoke change, completes minimal proposal/specs/design/adr/tasks artifacts, verifies `openspec status --change <smoke> --json`, verifies `openspec instructions apply --change <smoke> --json`, and safely removes only that smoke change unless `--keep-smoke` is passed.

Important:
- Do not run bare `openspec status --json`; OpenSpec 1.3.x requires `--change`.
- Report failures by layer: CLI, local schema, Codex prompt/skill adapter, generated skill, or project artifact.
- Default cleanup is safe and automatic only for a `zz-smoke-intent-overlay-*` change created by this command in the same run. Use `scripts/check-overlay --keep-smoke` to inspect it manually.
