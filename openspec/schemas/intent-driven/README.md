# Intent-Driven OpenSpec Schema for Codex

`intent-driven` is a Codex-adapted OpenSpec workflow for changes where intent,
observable behavior, technical design, context-aware review, ADR review, test-first verification planning, and implementation tasks
should all be captured before code changes.

## Activate

Set this in `openspec/config.yaml`:

```yaml
schema: intent-driven
```

## Lifecycle

```text
proposal -> specs -> design -> review -> adr -> test-plan -> tasks -> apply
```

- `proposal.md` captures why the change matters and which capabilities are affected.
- `specs/<capability>/spec.md` captures OpenSpec Markdown delta specs with
  normative `MUST`/`SHALL` requirement text and Gherkin-style scenarios.
- `design.md` explains implementation approach after specs exist.
- `review.md` is the mandatory `grill-with-docs` context review gate.
- `adr.md` is a per-change ADR review gate informed by `review.md`.
- `test-plan.md` maps requirements and scenarios to TDD or verification-first evidence.
- `tasks.md` is the dependency-ordered implementation checklist.
- `apply` reads the OpenSpec context files, follows review/test-plan constraints, and works through `tasks.md`.

## Persistent project context

Intent-driven Codex projects may include these root context files:

- `CONSTITUTION.md` — Git-tracked, non-secret project rules for required
  technologies, MCP servers, external systems, secret handling, documentation
  sources, verification rules, and additional AI instructions.
- `ARCHITECTURE.md` — current architecture snapshot for new chats and
  architecture-sensitive work.
- `adr/` — append-only durable architecture decision history.

These files are **not** OpenSpec artifacts:

- they are not listed in this schema's artifact graph;
- they are not archived with `openspec/changes/<change>/`;
- OpenSpec CLI does not read or enforce them by itself.

Codex `/opsx:*` prompts and supporting skills are responsible for reading the
constitution and architecture context before workflow actions, stopping on
material conflicts, and handling missing-context bootstrap rules. Schema
instructions only remind Codex to account for relevant context while preserving
OpenSpec's normal proposal/specs/design/review/adr/test-plan/tasks lifecycle.

Secret values do not belong in the constitution, architecture snapshot, ADRs, or
OpenSpec artifacts. Store local values such as logins, passwords, tokens, and
private service URLs in ignored `.secrets.local.env`; tracked files may contain
variable names and empty placeholders only.

## ADR model

Every change gets `openspec/changes/<change>/review.md`, `openspec/changes/<change>/adr.md`, and `openspec/changes/<change>/test-plan.md`. Durable architectural
records, when needed, are created separately under top-level `adr/` and are
append-only. Supersession is represented by new ADRs that reference old ADRs,
not by rewriting accepted history. When durable decisions change current
architecture, update root `ARCHITECTURE.md` as the current snapshot.

## Validate

```bash
openspec schema validate intent-driven
```
