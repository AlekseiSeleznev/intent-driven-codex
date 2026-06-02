## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — status: accepted; remains in force. This change preserves OpenSpec CLI as the lifecycle engine and implements reviewer orchestration in the Codex overlay/helper layer.
- `adr/0003-formalize-project-context-entrypoints.md` — status: accepted; remains in force. This change keeps `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, and top-level `adr/` as persistent project context outside OpenSpec artifacts and preserves local-secret boundaries.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — status: accepted; remains in force. This change does not replace mandatory `grill.md`, `design-review.md`, `test-plan.md`, or Matt TDD discipline.
- `adr/0002-adopt-project-constitution-preflight.md` — superseded by ADR 0003; considered only as historical context for constitution preflight and local-secret handling.
- `adr/0004-adopt-mandatory-review-and-test-plan-gates.md` — superseded by ADR 0005; considered only as historical context for mandatory review/test-plan evolution.

## Constitution / Architecture Rules Considered

- Required Technologies: preserve OpenSpec CLI 1.3.x-compatible public commands, project-local `.codex/` overlay, Markdown OpenSpec artifacts, Bash helper scripts, top-level durable ADRs, and root `ARCHITECTURE.md` as the current architecture snapshot.
- MCP Servers: no MCP server is required for this change; OpenSpec lifecycle work uses the local OpenSpec CLI.
- External Systems and Secret Handling: Claude auth/API credentials are external local state and must not be stored in tracked files. `.secrets.local.env` was not needed for planning and must not be read or printed for ordinary artifact review.
- Documentation Sources: project OpenSpec artifacts, root architecture snapshot, durable ADRs, local prompts/skills/scripts, and official Claude Code documentation for current CLI/model/effort behavior.
- Verification Rules: run OpenSpec validation, `openspec schema validate intent-driven` after schema updates, `scripts/check-overlay` after overlay/template updates, git status checks, and local secret staging checks before completion.
- Additional AI Instructions: stop on architecture conflicts and keep visible Git checkpoint boundaries. The user explicitly approved autonomous checkpoint commits and stepwise continuation for this change.

## Decisions Evaluated

- Add Claude Code artifact review as an optional Codex-overlay reviewer gate.
  - Outcome: accepted.
  - Rationale: it provides a second-model quality check for generated OpenSpec artifacts while preserving existing OpenSpec, grill, ADR, TDD, verify, and git gates.
- Implement review orchestration in `scripts/openspec-claude-review` and Codex prompts/skills rather than OpenSpec CLI.
  - Outcome: accepted.
  - Rationale: ADR 0001 requires the project-local overlay to use public OpenSpec commands without patching or forking OpenSpec.
- Use tracked non-secret JSON config with global defaults and per-stage overrides.
  - Outcome: accepted.
  - Rationale: stage-specific model/effort/budget policy is required by the user, and JSON can be parsed without new dependencies.
- Prefer pinned full model IDs while allowing aliases.
  - Outcome: accepted.
  - Rationale: pinned IDs improve reproducibility; aliases remain useful when projects intentionally want floating provider defaults.
- Request structured JSON/JSON-Schema output instead of free-form reviewer prose.
  - Outcome: accepted.
  - Rationale: workflow gating needs deterministic pass/warn/fail/blocked semantics.
- Keep review reports as auxiliary context under `openspec/changes/<change>/reviews/`, not schema lifecycle artifacts.
  - Outcome: accepted.
  - Rationale: this preserves the existing lifecycle graph and avoids replacing mandatory gates.
- Create a new durable top-level ADR for this integration.
  - Outcome: accepted.
  - Rationale: the change introduces a long-lived external reviewer integration pattern, model-selection policy, and workflow boundary that future maintainers need to understand.

## New Durable ADRs Created

- `adr/0006-adopt-claude-artifact-review.md` — status: accepted; records the durable decision to add optional Claude Code print-mode artifact review as a Codex-overlay reviewer gate with pinned-model-first, stage-specific configuration and structured reports.

## Superseded ADRs

- None. ADR 0006 references and extends ADR 0001, ADR 0003, and ADR 0005, but does not supersede them.

## Architecture Snapshot Updates

- `ARCHITECTURE.md` must be updated during implementation to list ADR 0006 as in force and to show the optional Claude review helper/config/report boundary in the system model.
- `adr/README.md` must be updated during implementation to include ADR 0006 in the current in-force ADR list.

## No ADR Needed

- N/A. A durable ADR is needed because this change adds a long-lived optional external reviewer integration to the Codex/OpenSpec workflow architecture and defines model/review policy that will affect future changes and installed projects.
