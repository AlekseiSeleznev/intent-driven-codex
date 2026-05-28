## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — status: accepted; remains in force. This change preserves the project-local Codex/OpenSpec overlay boundary, public OpenSpec CLI usage, project-local schema ownership, Codex prompt/skill ownership, and explicit git checkpoint discipline.
- `adr/0003-formalize-project-context-entrypoints.md` — status: accepted; remains in force. This change preserves root `CONSTITUTION.md`, root `ARCHITECTURE.md`, top-level `adr/`, `openspec/README.md` as a bridge, and local-only secret handling outside OpenSpec artifacts.
- `adr/0002-adopt-project-constitution-preflight.md` — status: accepted but superseded by ADR 0003; considered only as historical context for the preflight and local-secret boundary carried forward by ADR 0003.

## Constitution / Architecture Rules Considered

- Required Technologies: preserve OpenSpec CLI 1.3.x-compatible public commands, project-local `.codex/` overlay, Markdown OpenSpec artifacts with Gherkin-style scenarios, Bash helper scripts, top-level durable ADRs, and root `ARCHITECTURE.md` as the current snapshot.
- MCP Servers: no external MCP access is required for this change; OpenSpec lifecycle work uses the local OpenSpec CLI.
- External Systems and Secret Handling: no external systems or secret values are needed; `.secrets.local.env` remains local-only and must not be read, printed, staged, committed, archived, or copied for this work.
- Documentation Sources: project OpenSpec artifacts, root architecture snapshot, durable ADRs, README/AGENTS/install docs, and local prompts/skills are the relevant sources.
- Verification Rules: run OpenSpec validation, `openspec schema validate intent-driven` after schema updates, `scripts/check-overlay` after overlay/template updates, Git status checks, and explicit verification evidence before claiming completion.
- Additional AI Instructions: stop on project-wide architecture conflicts and prefer visible Git checkpoint boundaries. The user explicitly approved fast-forward planning and intermediate commits for this change.

## Decisions Evaluated

- Add `review.md` as a first-class OpenSpec artifact after `design.md` and before `adr.md`.
  - Outcome: accepted.
  - Rationale: context-aware `grill-with-docs` review must become a visible, checkpointable gate rather than chat-only guidance.
- Add `test-plan.md` as a first-class OpenSpec artifact after `adr.md` and before `tasks.md`.
  - Outcome: accepted.
  - Rationale: implementation tasks should be written only after a TDD or verification-first strategy maps requirements/scenarios to checks.
- Keep OpenSpec CLI unpatched and implement the lifecycle change in the project-local `intent-driven` schema and Codex overlay.
  - Outcome: accepted.
  - Rationale: ADR 0001 requires a local overlay that uses public OpenSpec commands and remains upgradeable.
- Make the review gate mandatory for development/enhancement changes but concise when no material questions remain.
  - Outcome: accepted.
  - Rationale: the gate should prevent missed context without inventing low-value ceremony.
- Require classic TDD when feasible, otherwise require the strongest verification-first substitute and explicit limitations.
  - Outcome: accepted.
  - Rationale: some target stacks and documentation/schema changes cannot support unit-test-first flow, but every change still needs planned verification before tasks.
- Treat dirty local tooling state classification as a separate follow-up change.
  - Outcome: accepted for this change scope.
  - Rationale: mixing it into lifecycle gate changes would obscure the architecture decision and complicate verification.

## New Durable ADRs Created

- `adr/0004-adopt-mandatory-review-and-test-plan-gates.md` — status: accepted; records the durable decision to expand the intent-driven lifecycle with mandatory `review.md` and `test-plan.md` gates, reference ADR 0001 and ADR 0003, and preserve project-local overlay boundaries.

## Superseded ADRs

- None. ADR 0004 references and extends ADR 0001 and ADR 0003, but does not supersede them.
- ADR 0002 remains superseded by ADR 0003 only.

## Architecture Snapshot Updates

- `ARCHITECTURE.md` must be updated during implementation to show the expanded lifecycle and ADR 0004 as in-force after the schema/prompt/skill updates are applied.
- `adr/README.md` must be updated during implementation to include ADR 0004 in the current in-force ADR list.

## No ADR Needed

- N/A. A durable ADR is needed because this change modifies the long-lived OpenSpec/Codex lifecycle architecture used by every new development or enhancement change and affects installed templates for new projects.
