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

## Compatibility check

```bash
scripts/check-overlay
```

The checker reports failures by layer:

- OpenSpec CLI;
- local schema;
- Codex prompt/skill adapter;
- generated skill / OpenSpec instruction behavior;
- project artifact.
