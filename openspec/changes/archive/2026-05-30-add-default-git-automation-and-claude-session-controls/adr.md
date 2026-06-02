## ADR Review

## Existing In-Force ADRs

- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — status: accepted; remains in force. This change preserves OpenSpec as the public CLI lifecycle engine and keeps workflow behavior in the Codex overlay. ADR 0007 narrows only ADR 0001's explicit user-approval rule for ordinary lifecycle checkpoint commits; ADR 0001 remains otherwise in force.
- `adr/0003-formalize-project-context-entrypoints.md` — status: accepted; remains in force. This change keeps `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, and top-level `adr/` as persistent project context outside OpenSpec artifacts, and keeps local-secret boundaries intact.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — status: accepted; remains in force. This change does not replace mandatory `grill.md`, `design-review.md`, `test-plan.md`, or Matt TDD discipline.
- `adr/0006-adopt-claude-artifact-review.md` — status: accepted; remains in force. This change extends the optional Claude artifact review model with session-scoped controls, and reuses the existing helper/config/report boundary rather than replacing it.
- `adr/0002-adopt-project-constitution-preflight.md` — superseded by ADR 0003; considered only as historical context for project preflight and local-secret handling.
- `adr/0004-adopt-mandatory-review-and-test-plan-gates.md` — superseded by ADR 0005; considered only as historical context for mandatory review/test-plan evolution.

## Constitution / Architecture Rules Considered

- Required Technologies: preserve OpenSpec CLI 1.3.x-compatible public commands, project-local `.codex/` overlay files, Markdown OpenSpec artifacts, Bash/Python-stdlib helper scripts, top-level durable ADRs, and root `ARCHITECTURE.md` as the current architecture snapshot.
- MCP Servers: no MCP server is required for this change; OpenSpec lifecycle work uses the local OpenSpec CLI.
- External Systems and Secret Handling: `.codex/session/openspec-session.json`, generated effective Claude review config, and tracked review settings must contain non-secret booleans, model IDs/aliases, effort levels, budgets, prompt profiles, and blocking policies only. Claude credentials, tokens, private endpoints, and `.secrets.local.env` values must not be read or stored for ordinary lifecycle review control.
- Documentation Sources: project OpenSpec artifacts, root architecture snapshot, durable ADRs, local prompts/skills/scripts, and the existing Claude artifact review ADR/spec/config are the primary sources for this decision.
- Verification Rules: run OpenSpec validation for changed artifacts, `openspec schema validate intent-driven` after schema/schema-guidance changes, `scripts/check-overlay` after overlay/template updates, helper/session validation tests, git status checks, and local secret staging checks before archive.
- Additional AI Instructions: keep OpenSpec as source of truth, preserve explicit hard gates, prefer small visible checkpoints, and stop on material constitution or architecture conflicts.

## Decisions Evaluated

- Make ordinary lifecycle checkpoint commits automatic by default.
  - Outcome: accepted, scoped to safe checkpoint commits only.
  - Rationale: the user explicitly wants git discipline to work by default without repeated approval prompts, while the specs and grill retain hard stops for dirty unrelated work and higher-risk git operations.
- Continue requiring explicit approval for push, merge, PR creation, archive, destructive git operations, hard-gate bypasses, and dirty unrelated work.
  - Outcome: accepted.
  - Rationale: automatic checkpoint mode should reduce repetitive local checkpoint friction, not become broad approval for irreversible or externally visible operations.
- Use a scoped checkpoint helper such as `scripts/openspec-git-checkpoint` rather than prompt-only staging instructions.
  - Outcome: accepted.
  - Rationale: deterministic path allowlists, status reporting, dry-run support, and fail-closed behavior are safer than relying only on prose guidance.
- Add ignored local session state at `.codex/session/openspec-session.json`.
  - Outcome: accepted.
  - Rationale: session intent for git automation and Claude review should be reversible, non-secret, local to the worktree/session, and not treated as project-global policy.
- Add Claude review control commands for status, enable, disable, reset, and per-stage settings.
  - Outcome: accepted.
  - Rationale: the user asked for `/opsx:*` controls, and commands are easier and safer than hand-editing the global tracked review config.
- Merge session Claude review settings over `.codex/openspec-claude-review.json` and feed an effective config to the existing review helper.
  - Outcome: accepted.
  - Rationale: this preserves ADR 0006's helper/config/report boundary and avoids duplicating model/effort validation or `claude -p` orchestration.
- Prompt for Claude review intent at key entry points when no session decision exists, then reuse the recorded decision.
  - Outcome: accepted.
  - Rationale: this satisfies the user's requirement to ask at new sessions/key workflows without adding repetitive prompts to every command.
- Store session overlays as OpenSpec lifecycle artifacts.
  - Outcome: rejected.
  - Rationale: session overlays are Codex-layer workflow context, not proposal/spec/design/test/task artifacts; they may be referenced only when an artifact intentionally records a decision, override, or verification outcome.
- Create a new durable top-level ADR for this workflow automation/control policy.
  - Outcome: accepted.
  - Rationale: default git checkpointing and session review control change long-lived workflow behavior and intentionally narrow a rule from ADR 0001.

## New Durable ADRs Created

- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` — status: accepted; records the durable decision to adopt scoped automatic lifecycle checkpoint commits by default and session-scoped Claude review controls over the tracked global reviewer configuration.

## Superseded ADRs

- None at file level. ADR 0007 amends/narrows ADR 0001's checkpoint approval rule for ordinary local lifecycle checkpoint commits, but ADR 0001 remains in force for the project-local overlay architecture and for explicit approval around higher-risk git operations.

## Architecture Snapshot Updates

- `ARCHITECTURE.md` must be updated during implementation to list ADR 0007 as in force, to document that ADR 0007 narrows ADR 0001's checkpoint-approval rule, and to show the `.codex/session/openspec-session.json` session-state boundary and `scripts/openspec-git-checkpoint` helper in the system model.
- `adr/README.md` must be updated during implementation to include ADR 0007 in the current in-force ADR list and explain that ADR 0001 remains otherwise in force.

## No ADR Needed

- N/A. A durable ADR is needed because this change alters default workflow safety/automation policy, introduces local session-state semantics, and defines how Claude review settings can be overridden per session without changing project-global defaults.
