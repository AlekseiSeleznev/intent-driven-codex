# OpenSpec update safety

This template is an overlay. It does not fork or patch OpenSpec.

## Ownership boundaries

| Layer | Owner | Update behavior |
|---|---|---|
| OpenSpec CLI | OpenSpec upstream | Update normally. |
| Built-in schemas | OpenSpec upstream | Do not edit in place. |
| `openspec/schemas/intent-driven` | Project/template | Validate after updates. |
| `.codex/prompts` and `.codex/skills` | Project/template | Merge intentionally. |
| `openspec/changes`, `openspec/specs`, `adr/NNNN-*` | Target project | Never overwritten by template update. |
| `CONSTITUTION.md` | Target project | Project-owned rules; never overwritten silently or archived with changes. |
| `ARCHITECTURE.md`, `adr/`, `docs/architecture/` | Target project | Current architecture and decision history; never overwritten silently. |
| `.secrets.local.env`, `.secrets.*.local.env` | Local environment | Ignored local secrets; never printed, copied, staged, committed, archived, or overwritten. |

## Project context and local secrets

`CONSTITUTION.md` is persistent project state. Overlay updates may propose a new
template or documentation, but they must not replace an existing constitution
without explicit project-owner approval. Treat constitution changes as project
rule changes, not as OpenSpec artifact churn.

`ARCHITECTURE.md` is the current architecture snapshot and `adr/` is durable
decision history. Update them intentionally when an accepted decision changes
architecture; do not overwrite them during template update or archive.

If a repository still has legacy `PROJECT_CONSTITUTION.md` and lacks
`CONSTITUTION.md`, report a migration opportunity. Do not silently create a
competing rule source with different content.

`.secrets.local.env` and related local secret files are outside the overlay and
outside OpenSpec. They contain local values for credential variable names listed
in the constitution. Update checks, install scripts, archive flows, and reports
must never display their values and must never stage or commit them.

Tracked examples such as `.secrets.example.env` may contain variable names,
comments, and empty placeholders only.

## Compatibility check

```bash
scripts/check-overlay
```

The checker reports failures by layer:

- OpenSpec CLI;
- local schema;
- Codex prompt/skill adapter;
- generated skill / OpenSpec instruction behavior;
- project artifact;
- project context setup guidance.
