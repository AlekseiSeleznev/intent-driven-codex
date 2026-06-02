# Update safety

Intent-Driven Codex is a project-local overlay. Do not patch installed OpenSpec
packages.

After schema, prompt, skill, or template updates, run:

```bash
openspec schema validate intent-driven
openspec validate --all --strict
scripts/check-overlay
```

`scripts/check-overlay` must prove that the `intent-driven` artifact graph is:

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


## Claude review safety

Optional Claude artifact review is part of the Codex overlay, not OpenSpec CLI.
After updating the overlay, `scripts/check-overlay` verifies that
`scripts/openspec-claude-review` exists, validates `.codex/openspec-claude-review.json`,
exercises dry-run review without contacting Claude, and checks that prompts and
skills require a user-facing `## Claude Review` block after each reviewer
invocation. That block must report `Budget / cost`, user-facing questions (or
`Вопросов, требующих участия пользователя: нет.`), a short verdict/summary, and
the disposition for findings before checkpointing. Do not checkpoint while any Claude finding is unhandled.

Tracked review configuration must stay non-secret. Claude auth, API keys,
private endpoints, and local transcripts remain outside Git and outside
OpenSpec artifacts.


## Session-state and checkpoint helpers

After updates, `scripts/check-overlay` must validate `.codex/session/` ignore behavior, `scripts/openspec-session-state`, `scripts/openspec-git-checkpoint`, Claude review config validation, and checkpoint dry-run fixtures without contacting live Claude or reading `.secrets.local.env`.

## Auto-repair shim safety

The auto-repair distribution is source-controlled in `bin/opsx`,
`bin/openspec-shim`, and `manifest.yaml`. `scripts/check-overlay` runs
`scripts/test-auto-repair-tooling all` to prove that:

- `opsx install-auto-repair --source-root <root> --share-root <root>` preserves
  the selected share/source root instead of deleting it before copy;
- the OpenSpec shim does not run repair for help/version/no-op commands such as
  `openspec update --help` or `openspec --version`;
- `opsx doctor` accepts canonical source docs (`docs/lifecycle.md`,
  `docs/update-safety.md`) while installed projects still receive/check
  Brownfield-safe aliases (`docs/intent-driven-lifecycle.md`,
  `docs/intent-driven-update-safety.md`);
- post-install sanity checks compare the shim-delegated OpenSpec version with the
  recorded real OpenSpec binary and fail clearly for stale NVM paths.

These checks use temporary fake executables only. They do not patch OpenSpec,
contact package registries, call Claude, or read `.secrets.local.env`.
