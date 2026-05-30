## Context Read

- `openspec/changes/add-default-git-automation-and-claude-session-controls/proposal.md` — intent, breaking checkpoint policy, Claude review session controls, impacted files and docs.
- Delta specs under `openspec/changes/add-default-git-automation-and-claude-session-controls/specs/` — behavior for automatic git checkpoints, `/opsx` workflow commands, Claude review session overlays, and schema guidance.
- `openspec/changes/add-default-git-automation-and-claude-session-controls/grill.md` — pre-design findings and ADR candidate.
- `openspec/changes/add-default-git-automation-and-claude-session-controls/design.md` — proposed session state, helper scripts, prompt commands, checkpoint helper, verification, migration, and rollback design.
- `CONSTITUTION.md` — required project-local OpenSpec/Codex overlay, Bash helper scripts, top-level ADRs, verification rules, and no-secret policy.
- `CONTEXT.md` — glossary boundaries; no changes needed because design terms remain workflow/policy concepts.
- `ARCHITECTURE.md` — current Codex overlay/OpenSpec/Claude review boundary model.
- `adr/README.md` and in-force ADRs 0001, 0003, 0005, 0006 — overlay architecture, project context, mandatory grill/TDD gates, optional Claude review boundary, and append-only ADR rules.
- `.gitignore` — current local-secret ignores and `.worktrees/` ignore; design adds `.codex/session/`.
- `.codex/openspec-claude-review.json` and `scripts/openspec-claude-review` — current tracked review defaults and reusable `--config` path.
- `.codex/prompts/opsx-*.md`, `.codex/skills/openspec-*/SKILL.md`, `scripts/check-overlay`, `scripts/install-overlay`, `README.md`, `README.ru.md`, `docs/lifecycle.md`, `docs/update-safety.md`, and `AGENTS.md` — current manual checkpoint and Claude review surfaces to update.

## Design Summary

- The design keeps OpenSpec CLI unchanged and implements all new behavior in the Codex overlay plus helper scripts.
- Session review settings and git checkpoint mode live in ignored local state at `.codex/session/openspec-session.json`, with a helper that validates and converts state into an effective Claude review config.
- Existing `scripts/openspec-claude-review --config <path>` remains the reviewer execution boundary; session behavior is injected by a generated temporary config rather than duplicating model/effort validation.
- Automatic checkpointing is implemented through a separate `scripts/openspec-git-checkpoint` helper that allowlists lifecycle-step paths and fails closed on unrelated dirty files.
- The design adds explicit `/opsx:claude-review-*` prompt files and updates entry-point prompts/skills, docs, installer, overlay checks, and durable ADR/architecture context.

## Question Loop

### Check 1: Is ignored local session state compatible with project no-secret and OpenSpec boundaries?

- **Recommended answer:** Yes, with validation. The state is Codex-layer local workflow context, ignored by Git, and must reject secret-looking keys/values. It should not be copied into OpenSpec artifacts or reports except when an artifact intentionally records a decision or override.
- **Rationale:** ADR 0003 keeps local state and secrets outside OpenSpec artifacts, and ADR 0006 already treats Claude auth as external local state. The design's ignored state file preserves that boundary.
- **Resolution:** Resolved by design. Test-plan should include checks that `.codex/session/` is ignored and that secret-looking state is rejected.
- **User question needed:** No.

### Check 2: Is generating a temporary effective Claude config better than extending the reviewer directly?

- **Recommended answer:** Yes. Keep `scripts/openspec-claude-review` focused on review execution and validation, and have `scripts/openspec-session-state` own session merge behavior.
- **Rationale:** This reduces coupling, reuses existing model/effort validation, and keeps global review config semantics stable.
- **Resolution:** Resolved by design. Implementation tasks should add tests for effective-config generation and then call the existing reviewer with `--config`.
- **User question needed:** No.

### Check 3: Is auto-commit safe enough with a separate checkpoint helper?

- **Recommended answer:** Yes, if the helper is fail-closed and path-scoped. It must use `git status --porcelain=v1`, stage only allowlisted paths, stop on any unrelated dirty path, and always show paths/message/hash.
- **Rationale:** Automatic commits are the riskiest part of the change. A dedicated helper makes the policy testable and avoids scattering ad hoc `git add` logic across prompts and skills.
- **Resolution:** Resolved by design. Test-plan must include dry-run/negative checks for unrelated dirty files and allowlisted artifact paths.
- **User question needed:** No.

### Check 4: Do the new `/opsx:claude-review-*` prompt names fit the existing prompt architecture?

- **Recommended answer:** Yes. Existing commands are mapped one-to-one to `.codex/prompts/opsx-*.md`; using `opsx-claude-review-status.md`, `opsx-claude-review-on.md`, `opsx-claude-review-off.md`, `opsx-claude-review-reset.md`, and `opsx-claude-review-set.md` keeps discovery predictable.
- **Rationale:** The names are longer than single-word commands but unambiguous and avoid overloading lifecycle commands.
- **Resolution:** Resolved by design. Documentation should list these commands in the command table.
- **User question needed:** No.

### Check 5: Is the ADR follow-up mandatory?

- **Recommended answer:** Yes. The design intentionally supersedes or narrows ADR 0001's explicit checkpoint approval rule for safe lifecycle checkpoint commits.
- **Rationale:** This is durable project policy, hard to reverse for installed overlays, surprising without context, and a real safety/friction trade-off.
- **Resolution:** Resolved. The next ADR artifact must create a durable ADR and update `ARCHITECTURE.md`/`adr/README.md` later during implementation tasks.
- **User question needed:** No.

## Design Findings

- **Finding 1 — Design preserves OpenSpec ownership.** All behavior remains in prompts, skills, and helper scripts; no OpenSpec CLI patching is proposed.
- **Finding 2 — Session state needs explicit ignore and validation coverage.** `.codex/session/` is a new local state surface and must be added to `.gitignore`, installer guidance, and overlay checks.
- **Finding 3 — Checkpoint helper must fail closed.** The helper should reject any dirty path outside the lifecycle allowlist before staging anything; this is the main safety control for automatic mode.
- **Finding 4 — Existing review helper can be reused.** The design's temporary effective config path avoids duplicating Claude model/effort validation and structured-report logic.
- **Finding 5 — Prompt/skill wording must be updated consistently.** Current prompt/skill text still says commits require explicit approval; implementation must update all listed surfaces to avoid conflicting instructions.
- **Finding 6 — Verification is feasible without live Claude calls.** Helper validation, dry-run review, effective-config generation, fixture parsing, and overlay smoke checks can verify behavior without reading `.secrets.local.env` or invoking a paid/live Claude review.

## Document Updates Applied

None during design review. The design is internally consistent and no proposal/spec/design edits were required before ADR planning.

## Document Updates Required Before Next Gate

None before `adr.md`.

The ADR artifact should explicitly record:

- automatic lifecycle checkpoint commits are the default only for safe scoped checkpoint boundaries;
- explicit approval remains required for push, merge, archive, PR creation, destructive operations, hard gates, and dirty unrelated work;
- `.codex/session/openspec-session.json` is ignored local non-secret Codex-layer state;
- Claude review session overlays merge over tracked project defaults and do not replace mandatory OpenSpec/TDD/ADR/grill gates.

## ADR Candidates

- **Adopt automatic lifecycle checkpoints and Claude review session controls** — durable ADR required. It should supersede or narrow ADR 0001's explicit-approval checkpoint rule while preserving the public OpenSpec CLI boundary, append-only ADR history, no-secret model, and optional Claude review constraints from ADRs 0001, 0003, 0005, and 0006.

## Open Questions

None.
