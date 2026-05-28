# Update safety

Intent-Driven Codex is a project-local overlay. Do not patch installed OpenSpec
packages.

After schema, prompt, skill, or template updates, run:

```bash
openspec schema validate intent-driven
openspec validate --all --strict
scripts/check-overlay
```

`script/check-overlay` must prove that the `intent-driven` artifact graph is:

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks
```

It must also prove that `openspec instructions apply --change <smoke> --json`
returns all hardened context files, including `grill`, `design-review`, and
`test-plan`.

Active changes created with the previous v0.1.2 lifecycle are not auto-migrated.
To use v0.1.3 behavior, migrate them by adding `grill.md` before design and
`design-review.md` after design, then refresh `adr.md`, `test-plan.md`, and
`tasks.md` from the new context.

Never overwrite user-owned target-project files silently during installation.
Use `scripts/install-overlay --force-overlay` only when replacing generated
placeholder overlay files is intended.
