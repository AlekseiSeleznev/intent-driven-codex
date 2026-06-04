## Verification Scope

Behavior-changing code paths:

- OpenAI-compatible artifact review helper configuration, request building, output parsing, report writing, cost summary, and exit policy.
- Session-state provider-neutral review controls.
- Lifecycle prompt/skill documentation that must stop invoking Claude review and use artifact review instead.
- README/architecture/ADR documentation and diagram updates.
- Local Polza credential storage and live smoke test when credentials are available.

## TDD Slices

### Slice 1: Config validation and skipped review

- **Behavior**: `scripts/openspec-artifact-review --validate-config` accepts safe provider-neutral config; disabled stages return skipped reports without provider calls.
- **RED command/evidence**: Run helper validation before implementation and observe missing command or failing validation.
- **GREEN command/evidence**: `scripts/openspec-artifact-review --validate-config --config .codex/openspec-artifact-review.json` exits 0; disabled dry run returns skipped/exit 3.
- **Refactor criteria**: Config validation functions are readable and do not duplicate secret checks unnecessarily.
- **Public interface**: CLI helper.
- **Mock boundaries**: No network for this slice.

### Slice 2: Parse OpenAI-compatible responses

- **Behavior**: Helper parses a saved Chat Completions response containing JSON review content and usage cost.
- **RED command/evidence**: Parse fixture fails before parser implementation.
- **GREEN command/evidence**: Parse fixture produces report with verdict, findings, token usage, and cost fields.
- **Refactor criteria**: Parser handles content as string JSON and reports schema errors deterministically.
- **Public interface**: `--parse-file` CLI.
- **Mock boundaries**: Static JSON fixture.

### Slice 3: Provider request dry run

- **Behavior**: Helper can build a Polza-compatible request without exposing the API key.
- **RED command/evidence**: Dry-run request output missing or includes secret-sensitive fields before implementation.
- **GREEN command/evidence**: `--dry-run` outputs provider base URL, endpoint, model, reviewed file manifest, and redacted credential status only.
- **Refactor criteria**: Request builder is isolated from network execution.
- **Public interface**: CLI dry run.
- **Mock boundaries**: No network.

### Slice 4: Session controls

- **Behavior**: Session-state helper supports `--review on|off|reset`, provider-neutral stage settings, and effective artifact review config.
- **RED command/evidence**: Commands fail before implementation.
- **GREEN command/evidence**: Commands update ignored session JSON with `artifactReview`; effective config validates; secret-looking keys are rejected.
- **Refactor criteria**: Legacy `claudeReview` validation remains tolerated but inactive.
- **Public interface**: `scripts/openspec-session-state`.
- **Mock boundaries**: Local temp session file.

### Slice 5: Lifecycle prompt replacement

- **Behavior**: Active `/opsx:*` prompts and `openspec-*` skills reference artifact review and do not instruct Claude invocation.
- **RED command/evidence**: Grep/check-overlay detects active Claude invocation guidance.
- **GREEN command/evidence**: Grep/check-overlay passes for required artifact-review markers and absence of active Claude invocation guidance.
- **Refactor criteria**: Legacy Claude files are clearly excluded or marked legacy-disabled.
- **Public interface**: repository text checks and `scripts/check-overlay`.
- **Mock boundaries**: None.

### Slice 6: Local live Polza smoke

- **Behavior**: With `POLZA_AI_API_KEY` in local `.secrets.local.env`, helper can call Polza using `deepseek/deepseek-v4-pro` and produce a structured review report.
- **RED command/evidence**: If credentials are absent, command reports missing variable name without value; if present before implementation, helper missing/fails.
- **GREEN command/evidence**: Live smoke review exits according to verdict policy and report contains no secret values.
- **Refactor criteria**: Live smoke output is concise and safe.
- **Public interface**: CLI helper with local environment.
- **Mock boundaries**: Real Polza API only for this slice.

## Required Verification Commands

- `openspec validate replace-claude-review-with-openai-compatible-review --strict`
- `scripts/openspec-artifact-review --validate-config --config .codex/openspec-artifact-review.json`
- `scripts/openspec-artifact-review --change <change> --stage specs --dry-run`
- `scripts/openspec-artifact-review --parse-file <fixture>`
- `scripts/openspec-session-state --validate`
- `scripts/check-overlay`
- `git check-ignore -v .secrets.local.env`
- `git status --short`

## Evidence Recording

During apply, record the actual RED/GREEN commands and results in the implementation summary. Do not mark tasks complete until the corresponding GREEN evidence exists or a limitation is explicitly reported.
