# ADR 0008: Adopt OpenAI-compatible artifact review

## Status

Accepted

## Supersedes

- `adr/0006-adopt-claude-artifact-review.md` for the active artifact reviewer implementation and provider boundary.
- `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md` only for Claude-specific review session controls. ADR 0007 remains in force for scoped automatic lifecycle checkpoints.

## Context

ADR 0006 adopted optional Claude Code print-mode artifact review through `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, and structured auxiliary reports. ADR 0007 added session-scoped Claude review controls.

The user now wants Claude review fully disabled from the active intent-driven workflow while keeping the legacy Claude code in the repository for compatibility. The replacement reviewer must be configurable through an OpenAI-compatible API: provider URL, API key environment variable, model, effort/reasoning options, budget, blocking policy, and report behavior. The immediate local provider is Polza.ai with `https://polza.ai/api/v1`, `POLZA_AI_API_KEY`, and model `deepseek/deepseek-v4-pro`.

## Decision

1. The active optional reviewer is provider-neutral OpenAI-compatible artifact review.
   - Active helper: `scripts/openspec-artifact-review`.
   - Active tracked config: `.codex/openspec-artifact-review.json`.
   - Active session field: `artifactReview` in ignored `.codex/session/openspec-session.json`.
   - Active prompts: `/opsx:review-status`, `/opsx:review-on`, `/opsx:review-off`, `/opsx:review-reset`, and `/opsx:review-set`.

2. Claude-specific review is legacy-disabled.
   - `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, and `/opsx:claude-review-*` files may remain in the codebase.
   - Current intent-driven lifecycle prompts and skills must not ask for, enable, or invoke Claude review.
   - Legacy `claudeReview` session state may be tolerated for old local files but does not enable active artifact review.

3. The active reviewer uses OpenAI-compatible Chat Completions first.
   - The provider base URL is configurable and defaults to `https://polza.ai/api/v1`.
   - The endpoint defaults to `chat/completions`.
   - The API key value is read from the configured environment variable, default `POLZA_AI_API_KEY`, and is never stored in Git-tracked files or reports.

4. Structured review semantics are preserved.
   - The reviewer returns `verdict`, `summary`, `mustFix`, `shouldFix`, and `questions`.
   - Reports remain auxiliary evidence under `openspec/changes/<change>/reviews/` and never replace proposal, specs, grill, design, design-review, ADR, test-plan, tasks, validation, or TDD evidence.
   - Blocking policy, skipped/unavailable behavior, finding disposition, user-facing `## Artifact Review` summaries, and cost/budget reporting continue to apply.

5. Prompt-only JSON is the default structured output mode.
   - Some OpenAI-compatible models, including the requested DeepSeek Pro route in Polza metadata, may not advertise JSON schema response-format support.
   - The helper can support response-format modes when configured, but it must validate parser-side and block safely on invalid output.

## Consequences

- Users can choose cheaper or local OpenAI-compatible providers without changing lifecycle semantics.
- Current documentation and diagrams no longer present Claude as the active reviewer.
- The repository carries legacy Claude files until a future cleanup change removes them.
- Provider/model parameter differences become a first-class configuration and validation concern.
- Secret handling remains aligned with ADR 0003: real keys live only in `.secrets.local.env` or local environment.

## Reconsider When

- OpenSpec provides a native external reviewer abstraction with equivalent secret safety and report semantics.
- The project decides to remove legacy Claude files entirely.
- A non-Chat-Completions API becomes necessary for the selected provider/model family.
