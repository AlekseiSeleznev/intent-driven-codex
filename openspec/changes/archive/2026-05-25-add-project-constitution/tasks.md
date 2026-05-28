## 1. Constitution assets and local secret safety

- [x] 1.1 Add root `PROJECT_CONSTITUTION.md` as a Git-tracked, non-secret starting file with sections for Required Technologies, MCP Servers, External Systems, Secret Handling, Documentation Sources, Verification Rules, and Additional AI Instructions.
- [x] 1.2 Add tracked `.secrets.example.env` with variable names/placeholders only for external-system credentials and no real values.
- [x] 1.3 Update `.gitignore` so `.secrets.local.env` and related local secret variants are ignored.
- [x] 1.4 Verify `git check-ignore .secrets.local.env` succeeds and that no secret values are present in tracked constitution/example files.

## 2. Shared constitution preflight skill

- [x] 2.1 Add `.codex/skills/project-constitution/SKILL.md` describing the shared constitution preflight workflow.
- [x] 2.2 Define missing-constitution behavior in the skill, including strict bootstrap-safe/diagnostic exceptions and explicit one-time override requirements.
- [x] 2.3 Define conflict handling in the skill so Codex stops before file changes or external-system calls when constitution rules contradict the request or OpenSpec artifacts.
- [x] 2.4 Define local secret handling in the skill so `.secrets.local.env` is read only when an external system listed in the constitution is actually needed, and secret values are never printed, staged, committed, archived, or logged.
- [x] 2.5 Define MCP and documentation-source handling in the skill so Codex uses constitution sections to choose relevant MCP servers, non-secret parameters, and documentation sources.

## 3. Opsx prompt and OpenSpec skill integration

- [x] 3.1 Update all `.codex/prompts/opsx-*.md` files with a concise constitution preflight rule near the top of each workflow.
- [x] 3.2 Update `openspec-new-change`, `openspec-continue-change`, `openspec-propose`, and `openspec-ff-change` skills to invoke or follow the shared constitution preflight before planning actions.
- [x] 3.3 Update `openspec-apply-change` and `openspec-bulk-apply-change` skills to run constitution preflight before implementation, goal handoff, worktree creation, subagent dispatch, or external-system access.
- [x] 3.4 Update `openspec-verify-change`, `openspec-archive-change`, `openspec-bulk-archive-change`, and `openspec-sync-specs` skills to enforce constitution preflight before verification, archive, sync, or bulk archive actions.
- [x] 3.5 Update `openspec-explore` and `openspec-check-overlay` skills to allow missing-constitution exploration/diagnostics while reporting setup guidance.

## 4. Intent-driven schema guidance

- [x] 4.1 Update `openspec/schemas/intent-driven/schema.yaml` artifact instructions so proposal, specs, design, adr, tasks, and apply guidance account for relevant `PROJECT_CONSTITUTION.md` rules without adding a constitution artifact.
- [x] 4.2 Update `openspec/schemas/intent-driven/README.md` and templates where useful to explain that constitution context is Codex-layer guidance, not OpenSpec CLI behavior.
- [x] 4.3 Run `openspec schema validate intent-driven` and fix any schema validation issues.

## 5. Documentation updates

- [x] 5.1 Update `README.md` and `README.ru.md` to explain project constitution purpose, required sections, `/opsx:*` preflight behavior, and local secret handling.
- [x] 5.2 Update `INSTALL_CODEX.md` to describe Greenfield/Brownfield constitution setup, `.secrets.local.env`, and safe example files.
- [x] 5.3 Update `AGENTS.md` to require Codex agents to read and respect `PROJECT_CONSTITUTION.md` before `/opsx:*` workflows.
- [x] 5.4 Update `docs/lifecycle.md` to show where constitution preflight runs in the lifecycle and how missing/conflicting constitution rules stop workflows.
- [x] 5.5 Update `docs/update-safety.md` to state that `PROJECT_CONSTITUTION.md` is project-owned and `.secrets.local.env` is local-only and must not be overwritten, archived, staged, or exposed.

## 6. Overlay checks and verification

- [x] 6.1 Update `scripts/check-overlay` if needed so overlay diagnostics may run without a constitution, report missing constitution as setup guidance, and never expose `.secrets.local.env` values.
- [x] 6.2 Run `openspec validate add-project-constitution --type change --strict`.
- [x] 6.3 Run `openspec schema validate intent-driven`.
- [x] 6.4 Run `scripts/check-overlay`.
- [x] 6.5 Verify OpenSpec status shows all planning artifacts complete and `openspec instructions apply --change add-project-constitution --json` is ready.
- [x] 6.6 Review `git status --short` and confirm no local secret files are staged or tracked.
