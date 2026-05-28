## ADR Review

## Existing In-Force ADRs
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — status: accepted; remains in force. This change extends the project-local Codex/OpenSpec overlay and keeps OpenSpec CLI externally owned and unpatched.

## Decisions Evaluated
- Adopt a shared Codex-layer project constitution preflight for `/opsx:*` prompts and direct `openspec-*` skill invocations.
- Keep `PROJECT_CONSTITUTION.md` as Git-tracked, non-secret project context outside the OpenSpec artifact lifecycle.
- Keep `.secrets.local.env` local-only and read it only when a workflow actually needs an external system listed in the constitution.
- Add constitution guidance to `intent-driven` schema instructions without adding a constitution artifact or changing the lifecycle graph.
- Reject YAML-only enforcement because schema instructions cannot perform runtime file reads, secret checks, MCP selection, or workflow stops.
- Reject patching OpenSpec CLI because ADR 0001 requires a project-local overlay and public OpenSpec CLI usage.

## New Durable ADRs Created
- `adr/0002-adopt-project-constitution-preflight.md` — status: accepted; records the durable decision to add project constitution preflight as a Codex-layer extension of ADR 0001.

## Superseded ADRs
- None. ADR 0001 remains in force and is referenced by ADR 0002; it is not superseded.

## No ADR Needed
- N/A. A durable ADR is needed because the change introduces a long-lived runtime boundary and policy for every `/opsx:*` workflow, local secret handling, and project-owned constitution state.
