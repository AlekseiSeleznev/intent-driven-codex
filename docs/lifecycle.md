# Intent-driven lifecycle

Intent-Driven Codex uses OpenSpec for lifecycle state and Codex for context,
grill, TDD, verification, and Git discipline.

## Planning order

| Artifact | File | Depends on | Purpose |
| --- | --- | --- | --- |
| proposal | `openspec/changes/<change>/proposal.md` | — | Intent, scope, capabilities, impact. |
| specs | `openspec/changes/<change>/specs/**/spec.md` | proposal | Observable OpenSpec requirements and scenarios. |
| grill | `openspec/changes/<change>/grill.md` | proposal, specs | Mandatory Matt `grill-with-docs` pre-design interrogation. |
| design | `openspec/changes/<change>/design.md` | grill | Technical approach after uncertainty is resolved. |
| design-review | `openspec/changes/<change>/design-review.md` | design | Mandatory Matt `grill-with-docs` post-design interrogation. |
| adr | `openspec/changes/<change>/adr.md` | design-review | Per-change ADR review and durable ADR references. |
| test-plan | `openspec/changes/<change>/test-plan.md` | adr | Matt TDD vertical RED/GREEN/REFACTOR slice plan. |
| tasks | `openspec/changes/<change>/tasks.md` | specs, grill, design, design-review, adr, test-plan | Dependency-ordered implementation checklist. |

Executable phases continue with:

```text
apply -> verify -> archive
```

## Grill gates

The `grill` and `design-review` artifacts use the vendored Matt
`grill-with-docs` skill.

Codex must:

1. read repository context before asking;
2. ask one material question at a time only when context cannot answer it;
3. include a recommended answer and rationale;
4. update `CONTEXT.md`, OpenSpec artifacts, or ADR candidates as decisions
   crystallize;
5. stop before the next gate unless `Open Questions` is `None` or a one-time
   override is recorded.

`CONTEXT.md` is glossary/domain language only. Project policy remains in
`CONSTITUTION.md`; current architecture remains in `ARCHITECTURE.md`; durable
rationale remains in `adr/`.

## TDD gate

The `test-plan` artifact uses the vendored Matt `tdd` skill. It defines vertical
slices:

```text
RED -> GREEN -> REFACTOR
```

Codex must not implement behavior-changing production code before observing the
corresponding failing test/check, unless `test-plan.md` records an explicit
pre-approved exception. Tests/checks should exercise public interfaces and
observable behavior rather than implementation details.

## Apply and verify

`/opsx:apply` reads all OpenSpec context files plus relevant root context and
then works one TDD slice at a time. A task is marked done only after RED/GREEN
evidence or an approved exception exists.

`/opsx:verify` checks task completion, requirement coverage, design adherence,
grill/design-review resolution integration, ADR compliance, and TDD evidence.
Missing RED/GREEN evidence for behavior-changing work is critical.

## Support skills

| Skill | Trigger | Rule |
| --- | --- | --- |
| `project-constitution` | before `/opsx:*` and lifecycle skills | read/enforce `CONSTITUTION.md`; use `CONTEXT.md`/`CONTEXT-MAP.md`, `ARCHITECTURE.md`, and ADRs when relevant |
| `grill-with-docs` | `grill.md`, `design-review.md` | automatic at gate; one material question at a time; update glossary/docs inline |
| `tdd` | `/opsx:apply` behavior changes, `test-plan.md` | automatic; vertical RED/GREEN/REFACTOR slices |
| `gherkin-authoring` | specs | observable OpenSpec scenarios |
| `c4-diagrams` | non-trivial boundaries | use Mermaid/ASCII diagrams or explicit no-diagram rationale |
| `architectural-decision-records` | ADR gate | durable ADRs in top-level `adr/`, append-only supersession |
| `openspec-git-discipline` | lifecycle + git history | checkpoints after artifacts and apply groups |
