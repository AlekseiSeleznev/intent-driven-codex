---
id: adr-0007
status: accepted
date: 2026-05-30
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/add-default-git-automation-and-claude-session-controls
related-adrs:
  - adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
  - adr/0003-formalize-project-context-entrypoints.md
  - adr/0005-adopt-matt-grill-and-tdd-gates.md
  - adr/0006-adopt-claude-artifact-review.md
supersedes: []
superseded_by: []
---

# 0007. Adopt Automatic Lifecycle Checkpoints and Claude Review Session Controls

## Status

Accepted.

## Context

ADR 0001 establishes `intent-driven-codex` as a project-local Codex/OpenSpec overlay: OpenSpec remains the public CLI lifecycle engine, while Codex prompts, skills, helper scripts, and project-local schema guidance own workflow behavior. ADR 0001 also requires mandatory git checkpoints after lifecycle artifacts and coherent apply groups, but says mutating git operations require explicit user approval.

ADR 0003 establishes root project context entry points and local-secret boundaries. ADR 0005 hardens the lifecycle with mandatory `grill.md`, `design-review.md`, and Matt TDD discipline. ADR 0006 adds optional Claude Code artifact review through `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, and auxiliary structured review reports.

The user now wants normal git discipline to work automatically by default unless explicitly disabled, and wants per-session Claude review controls for enablement, model, effort, budget, and blocking behavior. The change must reduce repetitive prompts without weakening the safety boundaries around dirty work, destructive actions, publication, archive, secrets, project rules, or mandatory OpenSpec gates.

## Decision

We will adopt **session-scoped workflow automation controls** in the Codex overlay.

The durable rules are:

1. Ordinary OpenSpec lifecycle checkpoint commits are automatic by default when session git discipline is in automatic mode.
   - This applies to scaffold, single-artifact, tracked review-report, and coherent apply task-group checkpoints.
   - Codex must show the affected paths, proposed commit message, resulting commit hash, and post-commit status.
   - Codex must stage only files that belong to the current lifecycle step or explicit allowlist.

2. Automatic checkpoint mode is not general approval for high-risk or externally visible operations.
   - Push, merge, pull request creation, archive, destructive git operations, hard-gate bypasses, external-system access, dirty unrelated work, and project-rule conflicts still stop for explicit approval or a documented one-time override.
   - A one-time override must name the bypassed gate, trusted files/state, accepted risk, and the next checkpoint where normal discipline resumes.

3. The overlay will add a deterministic checkpoint helper, such as `scripts/openspec-git-checkpoint`.
   - The helper owns path-scoped status checks, dry-run output, staging, commit creation, and fail-closed behavior when unrelated dirty paths are present.
   - Prompt and skill guidance may call this helper instead of hand-rolling equivalent git sequences.

4. The overlay will add ignored local non-secret session state at:

   ```text
   .codex/session/openspec-session.json
   ```

   The file records reversible workflow intent such as git checkpoint mode and Claude review session settings. It must be ignored by Git and must not contain credentials, tokens, private endpoints, or values from `.secrets.local.env`.

5. Claude review session controls will merge over the tracked global reviewer config.
   - `.codex/openspec-claude-review.json` remains the project-global non-secret default configuration from ADR 0006.
   - The session overlay can enable/disable review and set per-stage model, effort, budget, prompt profile, and blocking policy for the current session/worktree.
   - Session controls do not edit the global config unless the user explicitly asks for a global default change.
   - Generated effective configuration may be written to a temporary file and passed to `scripts/openspec-claude-review --config <path>`.

6. Codex will expose `/opsx:*` controls for Claude review session state.
   - Commands include status, enable, disable, reset/clear, and per-stage setting updates.
   - Key entry points such as `/opsx:new`, `/opsx:propose`, `/opsx:ff`, and `/opsx:apply` ask whether to enable Claude artifact review when no session decision exists, then reuse the recorded decision until changed or reset.

7. Session overlays are Codex-layer workflow context, not OpenSpec lifecycle artifacts.
   - They may influence artifact creation, apply, and verify behavior.
   - They must not replace proposal, specs, grill, design, design-review, ADR review, test-plan, tasks, OpenSpec validation, TDD evidence, or durable ADR rules.
   - They are recorded in artifacts only when the artifact intentionally documents a decision, override, or verification result.

This ADR **amends and narrows** ADR 0001's explicit-approval checkpoint rule only for ordinary local lifecycle checkpoint commits. ADR 0001 remains otherwise in force, including the project-local overlay architecture, OpenSpec CLI boundary, durable ADR model, update safety, and checkpoint discipline intent.

## Decision Drivers

- The user explicitly wants git discipline to work by default without repeated approval prompts, unless they request manual mode.
- Lifecycle artifacts must remain checkpointed before dependent artifacts or apply consume them.
- Dirty unrelated work and higher-risk git operations must remain visible and explicitly controlled.
- Claude review should be easy to enable, disable, and tune per session without changing tracked project defaults.
- Claude model and effort choices need per-stage flexibility while preserving ADR 0006's model/effort validation and disabled-safe defaults.
- Local session choices are not project-wide policy and should not churn Git history.
- OpenSpec must remain unpatched and accessed through public CLI commands.
- Secret values must stay out of tracked files, artifacts, review reports, and chat output.

## Considered Options

### Keep explicit approval for every checkpoint

Rejected. It preserves maximum operator control, but it keeps the repetitive friction the user specifically asked to remove. It also makes routine lifecycle continuation slower even when all modified paths are known, scoped, and recoverable.

### Make all git operations automatic after session opt-in

Rejected. Push, merge, PR creation, archive, destructive operations, and dirty unrelated work have broader consequences than local checkpoint commits. Treating automatic checkpoint mode as blanket approval would violate the safety intent of ADR 0001 and the constitution's conflict/secret/verification boundaries.

### Add prompt-only automatic checkpoint guidance

Rejected. Prompt text alone is too easy to apply inconsistently. A helper can make changed-path scoping, dry-run output, fail-closed behavior, and commit reporting deterministic.

### Store Claude session choices in the tracked global review config

Rejected. The global config is project policy. Per-session intent should be reversible and local, and should not create tracked config churn or accidentally enable Claude review for other sessions.

### Store session overlays as OpenSpec artifacts

Rejected. OpenSpec artifacts represent durable change intent, behavior, design, decisions, test plans, and tasks. Session overlays are ephemeral Codex-layer workflow context and should stay outside the OpenSpec artifact graph.

### Reuse the existing Claude review helper with an effective merged config

Accepted. This preserves the ADR 0006 boundary, avoids duplicated `claude -p` orchestration and model/effort validation, and lets session controls focus on state management and merge semantics.

## Consequences

### Positive

- Normal lifecycle work becomes faster because safe checkpoint commits no longer require repeated manual approval.
- Checkpoint state remains visible and auditable through affected-path, commit-message, commit-hash, and status reporting.
- Dirty unrelated work is less likely to be accidentally staged because automatic checkpoints must fail closed outside their scoped allowlist.
- Users can quickly enable, disable, inspect, or tune Claude review for one session without editing project-global config.
- Model/effort tuning can vary by lifecycle stage, preserving cost/quality balance while keeping validation centralized in the existing review helper.
- OpenSpec remains the lifecycle engine and does not need to know about Codex session state.
- No-secret boundaries remain intact because session state and generated effective configs are non-secret and ignored/local where appropriate.

### Negative / Trade-offs

- Automatic commits can surprise users who expect every mutating git operation to pause first; prompt and docs must make the narrowed approval policy clear.
- The overlay gains two new helper surfaces: session-state management and scoped checkpointing.
- Session state can become stale across terminals or worktrees unless status/reset commands are clear and cheap to run.
- Fail-closed path scoping can block a legitimate checkpoint until prompts/skills pass the right allowlist or the user applies an explicit one-time override.
- Per-stage Claude review controls increase configuration complexity and require good status output.

## Implementation References

- `openspec/changes/add-default-git-automation-and-claude-session-controls/`
- `scripts/openspec-git-checkpoint`
- `scripts/openspec-session-state`
- `scripts/openspec-claude-review`
- `.codex/openspec-claude-review.json`
- `.codex/session/openspec-session.json`
- `.codex/prompts/opsx-claude-review-status.md`
- `.codex/prompts/opsx-claude-review-on.md`
- `.codex/prompts/opsx-claude-review-off.md`
- `.codex/prompts/opsx-claude-review-reset.md`
- `.codex/prompts/opsx-claude-review-set.md`
- `.codex/prompts/opsx-*.md`
- `.codex/skills/openspec-*/SKILL.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `docs/lifecycle.md`
- `docs/update-safety.md`

## Follow-up Work

- Add `.codex/session/` to `.gitignore`.
- Implement and test `scripts/openspec-session-state`.
- Implement and test `scripts/openspec-git-checkpoint`.
- Update `/opsx:*` prompts and `openspec-*` skills to ask/reuse Claude review session intent and to use automatic checkpoint mode safely.
- Add Claude review session control prompts.
- Update `ARCHITECTURE.md` and `adr/README.md` to include ADR 0007 in the current architecture snapshot/index.
- Update docs, install scripts, overlay checks, specs, and verification guidance.

## Revisit Criteria

Revisit this ADR if:

- automatic checkpoints stage unrelated work or create confusing history in practice;
- Codex provides first-class session state and checkpoint policy primitives that can replace project-local helpers;
- OpenSpec adds native safe checkpoint hooks that satisfy the same boundary without patching;
- Claude review is generalized beyond Claude Code and needs a provider-neutral session control model;
- team or distribution feedback shows that automatic-by-default checkpointing is too surprising for installed projects.
