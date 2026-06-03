## 1. Apply Goal Guidance

- [x] 1.1 Update `.codex/prompts/opsx-apply.md` to run goal preflight after apply eligibility is known and before implementation file edits.
- [x] 1.2 Update `.codex/skills/openspec-apply-change/SKILL.md` with deterministic apply goal-generation conditions, skip conditions, and active-goal/nested-goal guidance.
- [x] 1.3 Add the generated apply `/goal` prompt shape, including literal `/opsx:apply <change>`, fallback to `openspec-apply-change`, completion criteria, stop-without-completion conditions, and separate approval for merge/archive/push/destructive actions.
- [x] 1.4 Verify the apply workflow still proceeds normally for small local changes or explicit user requests to continue without goal guidance.

## 2. Bulk Apply Goal Guidance

- [x] 2.1 Update `.codex/prompts/opsx-bulk-apply.md` to generate a parent `/goal` prompt for eligible multi-change runs before creating worktrees or dispatching subagents.
- [x] 2.2 Update `.codex/skills/openspec-bulk-apply-change/SKILL.md` with parent goal generation, active-goal skip behavior, explicit no-goal bypass behavior, and orchestration blocker handling.
- [x] 2.3 Add the generated bulk `/goal` prompt shape, including literal `/opsx:bulk-apply <changes...>`, fallback to `openspec-bulk-apply-change`, per-change verify/report completion criteria, and stop-without-completion conditions.
- [x] 2.4 Verify bulk apply still refuses single-change execution, still avoids parent-workspace implementation, and still forbids merge/archive/push without separate approval.

## 3. Documentation and Agent Guidance

- [x] 3.1 Update `AGENTS.md` to explain Codex Goal guidance as optional orchestration that does not replace OpenSpec artifacts or Git checkpoint gates.
- [x] 3.2 Update `README.md` with apply and bulk-apply goal-guidance behavior, examples of generated copy-paste `/goal` prompts, skip conditions, completion criteria, and stop conditions.
- [x] 3.3 Update `README.ru.md` with equivalent Russian guidance and examples, preserving exact command, skill, and file names.
- [x] 3.4 Update `docs/lifecycle.md` to include the goal-guidance preflight layer in the apply and bulk-apply runtime sequence.

## 4. Validation and Overlay Refresh

- [x] 4.1 Run `openspec validate add-codex-goal-guidance --strict` and resolve any change validation failures.
- [x] 4.2 Run `scripts/check-overlay` and resolve any prompt, skill, schema, or OpenSpec compatibility failures.
- [x] 4.3 Refresh the global Codex installation so updated prompts and skills are copied to `/home/as/.codex` using the project installer flow.
- [x] 4.4 Verify the refreshed global files contain the goal-guidance updates for `/opsx:apply`, `/opsx:bulk-apply`, `openspec-apply-change`, and `openspec-bulk-apply-change`.

## 5. Final Verification

- [x] 5.1 Confirm generated apply goal text starts with `/goal`, names the target change, includes `/opsx:apply <change>`, includes fallback to `openspec-apply-change`, and defines verify-gated completion.
- [x] 5.2 Confirm generated bulk goal text starts with `/goal`, names every target change, includes `/opsx:bulk-apply <changes...>`, includes fallback to `openspec-bulk-apply-change`, and defines parent report completion.
- [x] 5.3 Confirm stop-without-completion conditions cover dirty planning artifacts, blocked OpenSpec state, missing credentials/secrets, unavailable external services, external check failures, artifact contradictions, required design/spec/ADR decisions, worktree/subagent failures, and actions requiring explicit approval.
- [x] 5.4 Run `git status --short` and prepare the implementation checkpoint summary for review.
