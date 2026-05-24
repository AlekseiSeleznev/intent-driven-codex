# Installing intent-driven-codex

## Greenfield project

Use this when the target repository has no existing OpenSpec/Codex overlay decisions.

```bash
cd /path/to/new-project
openspec init . --tools codex --profile core

cd /home/as/ai-projects/intent-driven-codex
scripts/install-overlay /path/to/new-project

cd /path/to/new-project
openspec schema validate intent-driven
scripts/check-overlay
```

Expected result:

- `.codex/prompts/opsx-*.md` installed;
- `.codex/skills/*` installed;
- `openspec/schemas/intent-driven/` installed;
- `openspec/config.yaml` created with `schema: intent-driven` if it did not exist;
- `adr/README.md` created if absent;
- smoke check validates `proposal/specs/design/adr/tasks` apply context.

## Brownfield project

Use this when the target repository already has code, docs, OpenSpec specs, ADRs, or `.codex/` assets.

Before installing, inspect:

```bash
git status --short
find openspec -maxdepth 3 -type f 2>/dev/null | sort
find .codex -maxdepth 3 -type f 2>/dev/null | sort
find adr -maxdepth 2 -type f 2>/dev/null | sort
```

Then run the installer:

```bash
/path/to/intent-driven-codex/scripts/install-overlay /path/to/brownfield-project
```

The installer is no-overwrite:

- identical existing files are skipped;
- different existing files are reported as `SKIP existing`;
- active `openspec/changes/**` are not copied;
- canonical `openspec/specs/**` are not copied;
- existing `adr/**` is preserved;
- existing `.codex` customizations are preserved unless files are missing.

If `openspec/config.yaml` already exists, the installer does not replace it. Decide explicitly whether the project should switch to:

```yaml
schema: intent-driven
```

## Manual copy procedure

If you do not want to use the installer, copy only these overlay-owned paths:

```text
.codex/prompts/
.codex/skills/
openspec/schemas/intent-driven/
scripts/check-overlay
adr/README.md            # only if adr/ is absent or has no README
openspec/config.yaml     # only if absent or after explicit review
```

Never bulk-copy these from the template into a real project:

```text
openspec/changes/
openspec/specs/
adr/NNNN-*.md
source code
tests
```

## Post-install verification

```bash
openspec schemas --json
openspec schema validate intent-driven
scripts/check-overlay
```

`/opsx:check-overlay` runs the same compatibility path from Codex.
