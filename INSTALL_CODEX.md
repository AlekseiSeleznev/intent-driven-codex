# Installing intent-driven-codex

## Greenfield project

Use this when the target repository has no existing OpenSpec/Codex overlay decisions.

```bash
cd /path/to/new-project
openspec init . --tools codex --profile core

cd /path/to/intent-driven-codex
scripts/install-overlay /path/to/new-project --force-overlay

cd /path/to/new-project
openspec schema validate intent-driven
scripts/check-overlay
```

Expected result:

- `.codex/prompts/opsx-*.md` installed;
- `.codex/skills/*` installed;
- `openspec/schemas/intent-driven/` installed;
- `openspec/config.yaml` created with `schema: intent-driven` if it did not exist;
- `openspec/README.md` created as a bridge to persistent project context if absent;
- `adr/README.md` created if absent;
- `CONSTITUTION.md` and `ARCHITECTURE.md` installed only when absent;
- local secret values are kept in ignored `.secrets.local.env`, never in Git;
- smoke check validates `proposal/specs/grill/design/design-review/adr/test-plan/tasks` apply context.

After installation, review the project context files:

```bash
cp /path/to/intent-driven-codex/CONSTITUTION.md ./CONSTITUTION.md
cp /path/to/intent-driven-codex/ARCHITECTURE.md ./ARCHITECTURE.md
cp /path/to/intent-driven-codex/.secrets.example.env ./.secrets.example.env
touch .secrets.local.env
```

Only copy `CONSTITUTION.md` or `ARCHITECTURE.md` when the target project does
not already have one, or after explicit review by the project owner. Fill the
constitution with required technologies, MCP servers, external systems,
documentation sources, verification rules, and additional AI instructions for
the target project. Keep `ARCHITECTURE.md` as the current architecture snapshot
and link in-force ADRs from it.

Ensure local secret files are ignored:

```gitignore
.secrets.local.env
.secrets.*.local.env
.env.local
.env.*.local
```

## Brownfield project

Use this when the target repository already has code, docs, OpenSpec specs, ADRs, `.codex/` assets, or project context files.

Before installing, inspect:

```bash
git status --short
find openspec -maxdepth 3 -type f 2>/dev/null | sort
find .codex -maxdepth 3 -type f 2>/dev/null | sort
find adr -maxdepth 2 -type f 2>/dev/null | sort
ls -la CONSTITUTION.md ARCHITECTURE.md .secrets.local.env 2>/dev/null || true
ls -la PROJECT_CONSTITUTION.md 2>/dev/null || true  # legacy migration input
```

Then run the installer without `--force-overlay` first:

```bash
/path/to/intent-driven-codex/scripts/install-overlay /path/to/brownfield-project
```

Use `--force-overlay` only after reviewing that the existing `.codex/`, schema, and checker files are generated overlay files rather than project-owned customizations.

The installer is no-overwrite:

- identical existing files are skipped;
- different existing files are reported as `SKIP existing`;
- active `openspec/changes/**` are not copied;
- canonical `openspec/specs/**` are not copied;
- existing `adr/**` is preserved;
- existing `.codex` customizations are preserved unless files are missing;
- existing `CONSTITUTION.md`, `ARCHITECTURE.md`, and architecture docs are
  project-owned and must not be replaced silently;
- legacy `PROJECT_CONSTITUTION.md` should be migrated/renamed to
  `CONSTITUTION.md` after explicit review;
- existing `.secrets.local.env` is local-only and must never be copied,
  displayed, staged, or committed.

If `openspec/config.yaml` already exists, the installer does not replace it. Decide explicitly whether the project should switch to:

```yaml
schema: intent-driven
```

## Project context and local secrets

`CONSTITUTION.md` is Git-tracked project context for Codex. It is not an
OpenSpec change artifact and is not archived with changes. `/opsx:*` workflows
read it through the Codex-layer `project-constitution` preflight before planning,
apply, verify, sync, archive, or bulk operations.

The constitution should cover at least:

- Required Technologies;
- MCP Servers;
- External Systems;
- Secret Handling;
- Documentation Sources;
- Verification Rules;
- Additional AI Instructions.

`ARCHITECTURE.md` is the current architecture snapshot for new chats and
architecture-sensitive work. It should summarize current boundaries and link the
in-force durable ADRs under `adr/`. `openspec/README.md` is only a bridge that
points from OpenSpec lifecycle files to persistent project context outside the
OpenSpec artifact graph.

For external systems, put variable names in `CONSTITUTION.md` and local values
in `.secrets.local.env`:

```text
CONSTITUTION.md      # EXAMPLE_API_USERNAME, EXAMPLE_API_PASSWORD
.secrets.local.env   # real values; ignored by Git
.secrets.example.env # optional tracked placeholders only
```

Never commit real logins, passwords, tokens, or private service URLs. In
Brownfield projects, preserve existing constitution and architecture context
unless the owner explicitly approves changes to project-wide rules or current
architecture.

## Manual copy procedure

If you do not want to use the installer, copy only these overlay-owned paths:

```text
.codex/prompts/
.codex/skills/
openspec/schemas/intent-driven/
openspec/README.md       # only if absent or after explicit review
scripts/check-overlay
CONSTITUTION.md          # only if absent or after explicit project-owner review
ARCHITECTURE.md          # only if absent or after explicit project-owner review
CONTEXT.md               # only if absent or after explicit project-owner review
.secrets.example.env     # optional placeholders only; no secret values
adr/README.md            # only if adr/ is absent or has no README
openspec/config.yaml     # only if absent or after explicit review
```

Never bulk-copy these from the template into a real project:

```text
openspec/changes/
openspec/specs/
adr/NNNN-*.md
.secrets.local.env
.secrets.*.local.env
source code
tests
```

## Expanded lifecycle gates

New development/enhancement changes use:

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks
```

`grill.md` records mandatory pre-design Matt `grill-with-docs` review.
`design-review.md` records mandatory post-design Matt `grill-with-docs` review before ADR planning.
`test-plan.md` records vertical Matt TDD RED/GREEN/REFACTOR slices before implementation tasks. Existing active changes created with an older template should migrate these artifacts explicitly before apply.

## Post-install verification

```bash
openspec schemas --json
openspec schema validate intent-driven
scripts/check-overlay
git check-ignore .secrets.local.env
```

`/opsx:check-overlay` runs the same compatibility path from Codex.
