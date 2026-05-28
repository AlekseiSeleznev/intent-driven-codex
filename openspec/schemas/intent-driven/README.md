# intent-driven schema

The `intent-driven` schema is the project-local OpenSpec lifecycle used by
Intent-Driven Codex.

## Lifecycle

```text
proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks -> apply
```

The executable workflows continue after planning with:

```text
apply -> verify -> archive
```

## Artifacts

- `proposal.md` captures why the change exists, scope, capabilities, and impact.
- `specs/<capability>/spec.md` captures OpenSpec delta requirements with observable scenarios.
- `grill.md` is the mandatory pre-design Matt `grill-with-docs` gate.
- `design.md` describes the technical approach after pre-design uncertainty is resolved.
- `design-review.md` is the mandatory post-design Matt `grill-with-docs` gate.
- `adr.md` is the per-change ADR review gate informed by both grill gates.
- `test-plan.md` maps requirements/scenarios to vertical Matt TDD RED/GREEN/REFACTOR slices.
- `tasks.md` breaks implementation into dependency-ordered checkbox tasks.

## Project context files

These are Codex-layer project context, not OpenSpec change artifacts:

- `CONSTITUTION.md` — project policy, MCP/external systems, secrets, docs, verification rules.
- `CONTEXT.md` — glossary/domain language only.
- `CONTEXT-MAP.md` — optional multi-context glossary map.
- `ARCHITECTURE.md` — current architecture snapshot.
- `adr/` — durable decision history.
- `.secrets.local.env` — ignored local secret values, read only when a workflow needs a listed external system.

OpenSpec CLI does not read these files by itself. Codex prompts and skills are
responsible for the preflight and enforcement.

## Grill gates

`grill.md` and `design-review.md` use the vendored Matt Pocock
`grill-with-docs` skill. Codex runs it automatically at those gates. It asks one
material question at a time only when the answer cannot be found by reading the
repository. Resolved glossary terms are written to `CONTEXT.md`; durable
trade-offs become top-level ADR candidates.

A next gate must not consume a grill artifact unless `Open Questions` is `None`
or a one-time override is explicitly recorded.

## TDD gate

`test-plan.md` uses the vendored Matt Pocock `tdd` skill. It defines vertical
behavior slices:

```text
RED -> GREEN -> REFACTOR
```

Do not plan horizontal slices where all tests are written first and all
implementation follows later. `/opsx:apply` writes or identifies one behavior
check, observes the expected RED failure, implements the minimal GREEN, then
refactors only while green.

## Validation

Run these after schema or template changes:

```bash
openspec schema validate intent-driven
openspec validate --all --strict
scripts/check-overlay
```
