# Project Context Glossary

This file captures project language for `intent-driven-codex`. It is a glossary
only: no implementation details, no project policy, no architecture decisions,
and no secret values.

## Terms

### Intent-Driven Codex

A Codex-native overlay that helps a project run Intent-Driven Development with
OpenSpec as the lifecycle engine and source of truth.

### OpenSpec

The lifecycle engine used to create, validate, apply, and archive change
artifacts. OpenSpec orders artifacts and validates specs; it does not enforce
Codex-only project context such as the constitution or local secrets.

### Codex overlay

The project-local `.codex/prompts` and `.codex/skills` layer that tells Codex how
to operate OpenSpec workflows, project context preflight, grill gates, TDD,
verification, Git checkpoints, and installation checks.

### Project constitution

The root `CONSTITUTION.md` file. It contains project rules, required
technologies, MCP/external-system guidance, secret-handling policy,
documentation sources, verification rules, and additional AI instructions.

### Architecture snapshot

The root `ARCHITECTURE.md` file. It summarizes the current architecture state and
links to in-force ADRs. Durable rationale remains in `adr/`.

### Grill gate

A mandatory Matt `grill-with-docs` artifact gate that resolves material
uncertainty by reading context first and then asking one focused question at a
time only when needed. In v0.1.3, `grill.md` runs before design and
`design-review.md` runs after design.

### TDD slice

One vertical RED -> GREEN -> REFACTOR cycle through observable behavior and a
public interface. It is not a horizontal batch of all tests followed by all code.

### Design review gate

The post-design Matt `grill-with-docs` gate recorded in `design-review.md`. It
stress-tests the completed design before ADR and test planning.

### Source repository

The `intent-driven-codex` repository that authors, tests, and releases the reusable overlay. It may contain source-only regression helpers and canonical documentation paths that are not necessarily copied verbatim into installed projects.

### Installed project

A separate target repository that receives the intent-driven overlay through install, update, or repair tooling. Installed projects own their context, specs, ADRs, and application code, while template-owned overlay files may be repaired from the manifest.

### Auto-repair tooling

The `opsx` CLI, OpenSpec shim, repair manifest, bundled repair sources, and related diagnostics that restore template-owned overlay files after OpenSpec init or update commands without patching OpenSpec itself.

### Canonical docs

The source repository documentation paths `docs/lifecycle.md` and `docs/update-safety.md` used as the authored documentation source for release and regression checks.

### Installed doc aliases

The target-project documentation paths `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md` installed from canonical docs so brownfield projects can keep intent-driven documentation names distinct from project-owned docs.
