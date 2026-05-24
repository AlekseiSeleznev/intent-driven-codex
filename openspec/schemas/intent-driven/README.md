# Intent-Driven OpenSpec Schema for Codex

`intent-driven` is a Codex-adapted OpenSpec workflow for changes where intent,
observable behavior, technical design, ADR review, and implementation tasks
should all be captured before code changes.

## Activate

Set this in `openspec/config.yaml`:

```yaml
schema: intent-driven
```

## Lifecycle

```text
proposal -> specs -> design -> adr -> tasks
```

- `proposal.md` captures why the change matters and which capabilities are affected.
- `specs/<capability>/spec.md` captures OpenSpec Markdown delta specs with
  normative `MUST`/`SHALL` requirement text and Gherkin-style scenarios.
- `design.md` explains implementation approach after specs exist.
- `adr.md` is a per-change ADR review gate.
- `tasks.md` is the dependency-ordered implementation checklist.

## ADR model

Every change gets `openspec/changes/<change>/adr.md`. Durable architectural
records, when needed, are created separately under top-level `adr/` and are
append-only. Supersession is represented by new ADRs that reference old ADRs,
not by rewriting accepted history.

## Validate

```bash
openspec schema validate intent-driven
```
