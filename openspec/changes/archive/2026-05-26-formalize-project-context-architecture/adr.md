## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — status: accepted; remains in force. This change preserves the project-local Codex/OpenSpec overlay boundary, keeps OpenSpec CLI unpatched, and continues using public OpenSpec commands.
- `adr/0002-adopt-project-constitution-preflight.md` — status before this change: accepted; superseded by ADR 0003 for the canonical constitution filename and broader project-context entrypoint model. The Codex-layer preflight and local-secret boundary are carried forward.
- `adr/0003-formalize-project-context-entrypoints.md` — status: accepted; created by this change and becomes the in-force ADR for root `CONSTITUTION.md`, root `ARCHITECTURE.md`, OpenSpec bridge indexing, ADR-derived architecture snapshots, and local-secret boundaries.

## Constitution Rules Considered

- Required Technologies: preserve OpenSpec CLI 1.3.x-compatible public commands, project-local `.codex/` overlay, Markdown OpenSpec artifacts, Bash helper scripts, and top-level durable ADRs.
- MCP Servers: no external MCP access is required for planning this change; OpenSpec lifecycle work uses the local OpenSpec CLI.
- External Systems and Secret Handling: no external system access or secret values are needed; `.secrets.local.env` remains local-only and must not be printed, staged, committed, archived, or copied.
- Documentation Sources: use OpenSpec artifacts, top-level ADRs, project docs, and official docs only when current CLI/API behavior needs verification.
- Verification Rules: run changed OpenSpec validation, schema validation after schema updates, `scripts/check-overlay` after overlay updates, and Git status/secret staging checks before completion.
- Additional AI Instructions: stop on project-wide architecture conflicts and keep visible Git checkpoint boundaries; the user explicitly approved committing this fast-forward planning checkpoint.

## Decisions Evaluated

- Rename canonical constitution from `PROJECT_CONSTITUTION.md` to root `CONSTITUTION.md`.
  - Outcome: accepted.
  - Rationale: the user requested the shorter canonical name, and it is easier for new chats and installed projects to discover.
- Add root `ARCHITECTURE.md` as the current architecture snapshot.
  - Outcome: accepted.
  - Rationale: new chats need a concise current-state entry point; ADRs remain the detailed durable decision history.
- Keep durable ADRs under top-level `adr/`.
  - Outcome: accepted.
  - Rationale: ADR 0001 already established append-only durable ADR history outside OpenSpec archives.
- Add `openspec/README.md` as a bridge/index to persistent project context outside `openspec/changes/`.
  - Outcome: accepted.
  - Rationale: users and Codex need to understand how OpenSpec lifecycle files relate to root project context without moving policy/secrets into OpenSpec artifacts.
- Keep secret values in root local `.secrets.local.env`, not in OpenSpec, constitution, architecture, ADRs, docs, examples, screenshots, diffs, or chat output.
  - Outcome: accepted.
  - Rationale: preserves reviewable Git-tracked context while avoiding secret exposure.
- Create a new durable ADR for this context architecture.
  - Outcome: accepted.
  - Rationale: the change replaces the earlier constitution filename convention and adds a durable project context architecture that must survive future changes.

## New Durable ADRs Created

- `adr/0003-formalize-project-context-entrypoints.md` — status: accepted; records the decision to use root `CONSTITUTION.md`, root `ARCHITECTURE.md`, `openspec/README.md` as a bridge, top-level ADRs as history, Codex-layer context loading, and local-only secret values.

## Superseded ADRs

- `adr/0002-adopt-project-constitution-preflight.md` — superseded by `adr/0003-formalize-project-context-entrypoints.md`. ADR 0003 carries forward the Codex-layer preflight and local-secret boundary but replaces the `PROJECT_CONSTITUTION.md` canonical filename and expands the model to include `ARCHITECTURE.md` and an OpenSpec bridge.
- ADR 0001 is not superseded. ADR 0003 references and extends ADR 0001's project-local overlay boundary.

## No ADR Needed

- N/A. A durable ADR is needed because this change defines long-lived project context entry points, replaces the previous constitution filename convention, affects all `/opsx:*` workflows, and changes how new chats recover current architecture.
