## Context Read

- `CONSTITUTION.md` — project rules, required OpenSpec/Codex overlay technologies, no-secret policy, verification rules, and documentation-source priorities.
- `CONTEXT.md` — glossary terms for Intent-Driven Codex, OpenSpec, Codex overlay, project constitution, architecture snapshot, grill gate, TDD slice, and design review gate.
- `ARCHITECTURE.md` — current Codex overlay/OpenSpec boundary model, optional Claude review helper boundary, lifecycle order, and update rules.
- `adr/README.md` — in-force ADR set and append-only durable ADR rules.
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` — project-local overlay architecture and current explicit-approval git checkpoint decision, with revisit criterion for automated checkpoint discipline.
- `adr/0003-formalize-project-context-entrypoints.md` — root context model and no-secret boundaries.
- `adr/0005-adopt-matt-grill-and-tdd-gates.md` — mandatory `grill.md`/`design-review.md` and TDD gate discipline.
- `adr/0006-adopt-claude-artifact-review.md` — optional Claude reviewer gate, non-secret config, pinned-model-first guidance, structured reports, and no replacement of mandatory gates.
- `openspec/changes/add-default-git-automation-and-claude-session-controls/proposal.md` — change intent and scope.
- Delta specs under `openspec/changes/add-default-git-automation-and-claude-session-controls/specs/`:
  - `git-discipline/spec.md`
  - `codex-opsx-workflow/spec.md`
  - `claude-review-session-controls/spec.md`
  - `intent-driven-schema/spec.md`
- Canonical specs under `openspec/specs/` for `git-discipline`, `codex-opsx-workflow`, `claude-artifact-review`, and `intent-driven-schema`.
- `.codex/prompts/opsx-*.md` and `.codex/skills/openspec-*/SKILL.md` inventory and grep hits for checkpoint and Claude review behavior.
- `scripts/openspec-claude-review`, `scripts/check-overlay`, `scripts/install-overlay`, `docs/lifecycle.md`, `docs/update-safety.md`, `README.md`, `README.ru.md`, and `AGENTS.md` references to current checkpoint/review behavior.

## Plan Summary

- The change intentionally flips normal OpenSpec lifecycle checkpoint commits from manual approval by default to automatic-by-default, while preserving visible status/diff/commit reporting.
- The change narrows that automation to safe lifecycle checkpoints; dirty unrelated work, conflicting project rules, branch/merge/push/archive/destructive operations, and hard apply/archive gates remain explicit-stop or explicit-approval cases.
- The change adds session-level Claude artifact review controls over the canonical `.codex/openspec-claude-review.json` defaults, including enable/disable/status/set commands and per-stage model/effort overrides.
- Session overlays are non-secret Codex-layer workflow context, not OpenSpec lifecycle artifacts and not replacements for proposal/specs/grill/design/design-review/ADR/test-plan/tasks.
- Because this changes durable workflow policy from ADR 0001/AGENTS/manual checkpoint guidance, the later ADR artifact should create a new durable ADR that supersedes or narrows the explicit-approval part of the earlier decision.

## Question Loop

### Check 1: Does automatic git discipline override all mutating git operations?

- **Recommended answer:** No. Automatic mode should apply only to ordinary lifecycle checkpoint commits for scaffold/artifact/coherent apply task groups whose changed paths are scoped to the current step. Push, merge, PR creation, archive, destructive git operations, and dirty unrelated work must still stop for explicit approval or a one-time override.
- **Rationale:** This preserves the safety intent of ADR 0001 and current guardrails while removing repetitive approval friction for normal checkpoint commits.
- **Resolution:** Resolved from proposal/specs. The `git-discipline` delta explicitly keeps hard gates and visibility requirements and adds a dirty-unrelated-work block.
- **User question needed:** No.

### Check 2: Should the Claude review session overlay edit global project defaults?

- **Recommended answer:** No. Session commands should write or hold a session overlay that merges over `.codex/openspec-claude-review.json`; global defaults change only when the user explicitly asks for a global config edit.
- **Rationale:** The user asked for per-session controls as overlays of global settings, and ADR 0006 treats `.codex/openspec-claude-review.json` as tracked non-secret project policy.
- **Resolution:** Resolved from proposal/specs. The new `claude-review-session-controls` capability requires a reversible non-secret session overlay and the opsx workflow spec keeps global config unchanged by default.
- **User question needed:** No.

### Check 3: Should Codex ask about Claude review on every command?

- **Recommended answer:** No. Ask once at session start or key entry points when no explicit session decision exists; then reuse the recorded decision until the user changes or clears it.
- **Rationale:** Repeated prompts would recreate the friction this change is trying to remove, while never asking would fail the user's requirement for explicit review intent at new sessions and important workflow entry points.
- **Resolution:** Resolved from proposal/specs. The delta specs require prompting at `/opsx:new`, `/opsx:propose`, `/opsx:ff`, and `/opsx:apply` when no decision exists, and reusing the decision afterward.
- **User question needed:** No.

### Check 4: Is a durable ADR required later?

- **Recommended answer:** Yes. The change should create a durable ADR later because it changes the long-lived default git checkpoint policy and session review control model.
- **Rationale:** The decision is hard to reverse once published to installed overlays, surprising without context because current ADR/guidance says explicit approval, and it is a real trade-off between safety/visibility and workflow friction.
- **Resolution:** Record as an ADR candidate for the `adr.md` artifact. Do not rewrite ADR 0001; supersede or narrow the explicit-approval rule with a new ADR.
- **User question needed:** No.

### Check 5: Does this require glossary updates?

- **Recommended answer:** No immediate `CONTEXT.md` update. Terms such as “session overlay” and “automatic checkpoint mode” are workflow design terms for this change, not stable domain glossary terms yet.
- **Rationale:** `CONTEXT.md` is glossary-only; implementation policy and workflow semantics belong in specs, design, ADRs, and docs.
- **Resolution:** No glossary update applied. Revisit only if the design standardizes a new project-wide term that future users must distinguish from existing terms.
- **User question needed:** No.

## Resolved Terms

- **Automatic git discipline / automatic checkpoint mode** — resolved as safe, visible, automatic lifecycle checkpoint commits only; it does not authorize push, merge, archive, destructive git operations, or bypass hard gates.
- **Claude review session overlay** — resolved as non-secret Codex-layer session context that merges over `.codex/openspec-claude-review.json` without replacing project-global defaults.
- **Review-control commands** — resolved as Codex `/opsx:*` entry points for status, enable, disable, reset/clear, and set operations around Claude review session settings.

No `CONTEXT.md` update was applied because the resolved terms are workflow/policy concepts rather than domain glossary language.

## Document Updates Applied

- None during this grill artifact.
- The prior specs step already corrected `proposal.md` to reference canonical `claude-artifact-review` after `add-claude-artifact-review` was archived.

## Document Updates Required Before Next Gate

None before `design.md`.

Design should explicitly address, but is not blocked by:

- the concrete storage/location/lifetime for session overlays;
- how review-control `/opsx:*` prompts map to actual `.codex/prompts/opsx-*.md` files or command names;
- how automatic checkpoint mode determines changed-path scope and commit messages;
- how global/manual/session override precedence is represented in prompts, skills, docs, and helper behavior.

## ADR Candidates

- **Adopt automatic lifecycle checkpoints and Claude review session controls** — likely durable ADR. It should supersede or narrow the explicit user-approval checkpoint part of ADR 0001 while preserving explicit approval for push, merge, archive, destructive git operations, and hard safety gates. It should also record the non-secret session overlay model for Claude review controls.

## Open Questions

None.
