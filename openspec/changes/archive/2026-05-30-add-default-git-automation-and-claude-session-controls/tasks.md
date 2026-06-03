<!-- Implementation tasks preserve CONSTITUTION.md no-secret rules, ARCHITECTURE.md/ADR boundaries, mandatory grill/design-review/TDD gates, and OpenSpec CLI ownership. Do not expose, stage, or commit secret values. -->

## 1. Session State Helper and Local-State Guardrails

- [x] 1.1 Capture RED evidence for test-plan slice 1: `scripts/openspec-session-state --status`, `--validate`, and `.codex/session/openspec-session.json` ignore checks fail because the helper/ignore rule is missing.
- [x] 1.2 Add `.codex/session/` to `.gitignore` and implement the minimal executable `scripts/openspec-session-state` default/status/validate behavior with git checkpoint mode `auto` and Claude review decision `unset`.
- [x] 1.3 Capture GREEN evidence for test-plan slice 1 and update the test-plan Evidence Log, verifying no tracked `.codex/session/` files are created.
- [x] 1.4 Capture RED evidence for test-plan slice 2: session mutation flags for `--git-mode`, `--claude-review`, `--set-stage`, reset, and test-only `CODEX_OPENSPEC_SESSION_FILE` state persistence are not yet supported.
- [x] 1.5 Implement session mutation support for git mode, Claude review on/off/reset, stage overrides, timestamp/schema fields, and deterministic status output without editing `.codex/openspec-claude-review.json`.
- [x] 1.6 Capture GREEN evidence for test-plan slice 2 and update the test-plan Evidence Log, including reset behavior returning Claude review decision to `unset`.
- [x] 1.7 Capture RED evidence for test-plan slice 3: a deliberately bad local session file with a secret-looking key is not yet rejected by validation.
- [x] 1.8 Implement secret-looking key/path validation in `scripts/openspec-session-state --validate`, reporting only field names/paths and never values.
- [x] 1.9 Capture GREEN evidence for test-plan slice 3, refactor the helper only while GREEN, and re-run `bash -n scripts/openspec-session-state`.

## 2. Claude Review Session Overlay and Command Prompts

- [x] 2.1 Capture RED evidence for test-plan slice 4: effective Claude config generation with `--effective-claude-config --out <tmp>` is missing or cannot validate through `scripts/openspec-claude-review --validate-config --config <tmp>`.
- [x] 2.2 Implement deep-merge effective config generation from `.codex/openspec-claude-review.json` plus session enablement/stage overrides, writing only to the requested output path.
- [x] 2.3 Capture GREEN evidence for test-plan slice 4 and update the test-plan Evidence Log, verifying no live `claude -p` call and no `.secrets.local.env` read are required.
- [x] 2.4 Capture RED evidence for test-plan slice 5: `.codex/prompts/opsx-claude-review-{status,on,off,reset,set}.md` are missing or not wired to `scripts/openspec-session-state`.
- [x] 2.5 Add the five Claude review session control prompt files with project-constitution preflight, session-only behavior, non-secret status output, and explicit global-config edit boundaries.
- [x] 2.6 Capture GREEN evidence for test-plan slice 5 and update the test-plan Evidence Log, verifying prompt names, helper flags, and no-secret language are consistent.
- [x] 2.7 Re-run `scripts/openspec-claude-review --validate-config` to confirm existing global Claude review config still validates after session overlay support.

## 3. Entrypoint Prompts, Skills, and Git Policy Text

- [x] 3.1 Capture RED evidence for test-plan slice 6: `/opsx:new`, `/opsx:propose`, `/opsx:ff`, `/opsx:apply`, and `/opsx:continue` prompt/skill text lacks session-intent guidance.
- [x] 3.2 Update `.codex/prompts/opsx-new.md`, `opsx-propose.md`, `opsx-ff.md`, `opsx-apply.md`, and `opsx-continue.md` to ask for Claude review session intent only when unset, reuse recorded state, and avoid repeated prompts in continue flows.
- [x] 3.3 Update corresponding `openspec-*` skills so lifecycle workflows generate effective Claude review config before enabled reviews and read session/report context where required.
- [x] 3.4 Capture GREEN evidence for test-plan slice 6 and update the test-plan Evidence Log, checking prompt/skill wording does not conflict across entry points.
- [x] 3.5 Capture RED evidence for test-plan slice 9: unconditional manual-approval wording for safe lifecycle checkpoint commits remains in prompts, skills, `AGENTS.md`, docs, or git-discipline guidance.
- [x] 3.6 Update `.codex/skills/openspec-git-discipline/SKILL.md`, lifecycle skills, `.codex/prompts/opsx-*`, `AGENTS.md`, `CONSTITUTION.md`, `README.md`, `README.ru.md`, `docs/lifecycle.md`, and `docs/update-safety.md` so safe lifecycle checkpoints are automatic by default/manual opt-out while push, merge, PR, archive, destructive operations, hard-gate bypasses, and dirty unrelated work still require explicit approval.
- [x] 3.7 Capture GREEN evidence for test-plan slice 9 and update the test-plan Evidence Log, noting any archive-only canonical spec synchronization separately from pre-archive implementation docs.

## 4. Scoped Git Checkpoint Helper

- [x] 4.1 Capture RED evidence for test-plan slice 7: `scripts/openspec-git-checkpoint --dry-run` is missing or cannot report the allowlisted `test-plan` artifact path in a disposable Git repository.
- [x] 4.2 Implement executable `scripts/openspec-git-checkpoint` with `--change`, `--stage`, `--message`, repeated `--include`, and `--dry-run` options using only public Git commands.
- [x] 4.3 Implement lifecycle-step allowlists for scaffold, proposal, specs, grill, design, design-review, adr, test-plan, tasks, review reports, and explicit apply-group includes.
- [x] 4.4 Capture GREEN evidence for test-plan slice 7 and update the test-plan Evidence Log, verifying dry-run is side-effect free and reports staged paths plus commit message.
- [x] 4.5 Capture RED evidence for test-plan slice 8: a disposable repo with an unrelated dirty file must fail before staging anything.
- [x] 4.6 Implement fail-closed dirty-path detection with `git status --porcelain=v1`, clear unrelated-path reporting, commit-hash output on non-dry-run commits, and post-commit `git status --short` reporting.
- [x] 4.7 Capture GREEN evidence for test-plan slice 8, refactor only while helper checks remain GREEN, and re-run `bash -n scripts/openspec-git-checkpoint`.

## 5. Overlay Checks, Installer, Architecture Snapshot, and ADR Index

- [x] 5.1 Capture RED evidence for test-plan slice 10: `scripts/check-overlay --no-smoke` does not yet check `.codex/session/`, session-state helper behavior, or git-checkpoint helper dry-runs.
- [x] 5.2 Update `scripts/check-overlay` to verify `.codex/session/` ignore behavior, helper executability/syntax, session validation, effective-config generation, checkpoint dry-run fixtures, Claude config validation, and the intent-driven lifecycle graph.
- [x] 5.3 Update `scripts/install-overlay` so installed projects receive the new prompt files, helper scripts, docs, and ignore guidance without overwriting user-owned local session state.
- [x] 5.4 Capture GREEN evidence for test-plan slice 10 and update the test-plan Evidence Log, running full `scripts/check-overlay` when the smoke path is ready.
- [x] 5.5 Capture RED evidence for test-plan slice 11: `ARCHITECTURE.md` and `adr/README.md` do not yet list ADR 0007/current session-state and checkpoint-helper boundaries.
- [x] 5.6 Update `ARCHITECTURE.md` to list ADR 0007 as in force, document that it narrows ADR 0001 only for ordinary local lifecycle checkpoint commits, and add session-state/checkpoint-helper nodes to the system model.
- [x] 5.7 Update `adr/README.md` to list ADR 0007 in the current in-force durable ADR set while keeping ADR 0001 otherwise in force.
- [x] 5.8 Capture GREEN evidence for test-plan slice 11 and update the test-plan Evidence Log, without rewriting accepted ADR bodies.

## 6. Final Validation, Evidence, and Apply Readiness

- [x] 6.1 Ensure the test-plan Evidence Log has RED/GREEN/refactor notes for slices 1 through 12, with command outputs or concise references for each completed slice.
- [x] 6.2 Run `openspec validate add-default-git-automation-and-claude-session-controls --strict` and fix any artifact/spec validation issues.
- [x] 6.3 Run `openspec schema validate intent-driven` after schema, schema guidance, or lifecycle instruction changes and fix any schema validation issues.
- [x] 6.4 Run `scripts/openspec-claude-review --validate-config` and `scripts/openspec-claude-review --change add-default-git-automation-and-claude-session-controls --stage verify --dry-run` without contacting live Claude.
- [x] 6.5 Run tracked-secret checks so `git ls-files -- .secrets.local.env .secrets.*.local.env .env.local .env.*.local` returns no tracked local secret env files.
- [x] 6.6 Run `git status --short` before and after each coherent apply-group checkpoint and ensure no uncommitted planning, implementation, review, or local session files remain.
- [x] 6.7 Complete the final `tasks.md` checkbox updates only after all required evidence exists, then prepare for `/opsx:verify` and archive according to OpenSpec/git discipline.

## 7. Claude Review Budget Exhaustion Safety

- [x] 7.1 Confirm official Claude Code behavior for budget caps: `--max-budget-usd` applies in print mode, and agent results use subtype `error_max_budget_usd` when the configured budget is reached.
- [x] 7.2 Capture RED evidence with a local `error_max_budget_usd` fixture: the review helper does not yet classify it as budget exhaustion and does not disable session review.
- [x] 7.3 Update `scripts/openspec-claude-review` to detect Claude Code `error_max_budget_usd` / maximum-budget messages, return a blocking budget-exhausted report, and disable session review through `scripts/openspec-session-state --claude-review off`.
- [x] 7.4 Add `scripts/check-overlay` fixture coverage so budget-exhausted envelopes block and disable a temp session without contacting live Claude.
- [x] 7.5 Update proposal/spec/design/test-plan/docs to document the stop/report/auto-disable behavior and rerun required validations.
