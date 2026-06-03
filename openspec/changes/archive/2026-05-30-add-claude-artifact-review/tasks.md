## 1. Helper Config and TDD Foundation

- [x] 1.1 Add tracked non-secret `.codex/openspec-claude-review.json` with top-level `enabled: false`, balanced stage defaults, pinned model IDs, per-stage effort/budget policy, and no secret values.
- [x] 1.2 Add `scripts/openspec-claude-review` skeleton with `--help`, root resolution, `--validate-config`, `--dry-run`, `--change`, `--stage`, and `--config` arguments.
- [x] 1.3 RED/GREEN slice 1: before implementing validation behavior, run the missing/failing `scripts/openspec-claude-review --validate-config`; then implement config validation until it passes with `bash -n scripts/openspec-claude-review` and the default config.

## 2. Model, Effort, and Stage Resolution

- [x] 2.1 Implement config merge logic for global defaults plus per-stage overrides, including disabled global/stage behavior and review report path resolution.
- [x] 2.2 RED/GREEN slice 2: add temporary valid/invalid config checks for Opus `xhigh`, Sonnet `high`, Sonnet `xhigh` strict rejection, and Haiku no-effort handling before/while implementing model-aware effort validation.
- [x] 2.3 Implement optional `modelCapabilities` override support for custom model selectors without storing secrets or private endpoints.
- [x] 2.4 Ensure dry-run output records whether the configured model is a pinned ID or floating alias and warns when alias resolution may vary.

## 3. Review Input Bundle and Structured Report Handling

- [x] 3.1 Implement stage-specific file selection for proposal, specs, grill, design, design-review, adr, test-plan, tasks, and verify according to `design.md`.
- [x] 3.2 Implement secret/local-state denylist for `.secrets.local.env`, `.secrets.*.local.env`, `.env.local`, `.env.*.local`, Claude local transcripts/state, and unrelated oversized files.
- [x] 3.3 RED/GREEN slice 3: run a stage dry-run against `add-claude-artifact-review` before bundle support is complete; then implement deterministic bundle/manifest output until dry-run lists selected files, hashes, model, effort, budget, and report path without calling Claude.
- [x] 3.4 Implement structured review schema/report writing under `openspec/changes/<change>/reviews/<stage>-claude-review.json`.
- [x] 3.5 RED/GREEN slice 4: implement fixture parser checks for pass, fail, and invalid JSON outcomes, including policy-aware exit statuses and no free-form-prose inference.

## 4. Claude Invocation Path

- [x] 4.1 Implement the live invocation path using `claude -p`, stdin prompt bundles, `--output-format json`, `--json-schema`, configured `--model`, compatible `--effort`, `--fallback-model`, `--max-budget-usd`, `--permission-mode dontAsk`, and tools disabled by default.
- [x] 4.2 Ensure the live invocation path is skipped in `--dry-run`, `--validate-config`, and fixture parser modes.
- [x] 4.3 Implement unavailable-command/auth/error handling according to per-stage unavailable policy without printing secret values.
- [x] 4.4 Document that automated checks must not make live Claude calls; optional live smoke requires explicit user approval.

## 5. Codex Workflow Integration

- [x] 5.1 Update `.codex/skills/openspec-continue-change/SKILL.md` to run/account for `scripts/openspec-claude-review` after artifact creation and before dependent artifacts when config enables the stage.
- [x] 5.2 Update `.codex/skills/openspec-propose/SKILL.md` and `.codex/skills/openspec-ff-change/SKILL.md` so multi-artifact runs stop on blocking Claude review verdicts and checkpoint reports with artifacts.
- [x] 5.3 Update `.codex/skills/openspec-verify-change/SKILL.md` to include required Claude review reports and blocking verdicts in verification findings.
- [x] 5.4 Update `.codex/skills/openspec-apply-change/SKILL.md` to read review reports as auxiliary context when present without treating them as replacements for required artifacts.
- [x] 5.5 Update relevant `/opsx:*` prompts (`opsx-continue.md`, `opsx-propose.md`, `opsx-ff.md`, `opsx-verify.md`, and apply guidance if needed) with concise matching instructions.
- [x] 5.6 RED/GREEN slice 5: use `grep -R "openspec-claude-review" .codex/prompts .codex/skills` as the integration check, starting from failing/missing references and ending with required prompt/skill coverage.

## 6. Overlay Checks, Documentation, and Architecture Context

- [x] 6.1 Update `scripts/check-overlay` to verify the helper exists, is executable, passes `bash -n`, validates config, and supports dry-run/config validation without contacting Claude.
- [x] 6.2 Update `scripts/install-overlay` so target projects receive the helper/config and relevant review assets without overwriting user-owned files unexpectedly.
- [x] 6.3 Update docs (`README.md`, `README.ru.md`, `docs/lifecycle.md`, `docs/update-safety.md`, and schema README if needed) to explain enabling Claude review, pinned IDs vs aliases, stage model/effort recommendations, budgets, disabled default, reports, and no-secret rules.
- [x] 6.4 Update `adr/README.md` to list `adr/0006-adopt-claude-artifact-review.md` as in force.
- [x] 6.5 Update `ARCHITECTURE.md` to list ADR 0006 and show the optional helper/config/report boundary in the current architecture snapshot after implementation files exist.
- [x] 6.6 RED/GREEN slice 6: run targeted docs/check-overlay checks before and after updates, ending with `scripts/check-overlay --no-smoke` and full `scripts/check-overlay` passing.

## 7. Final Verification and Checkpoint

- [x] 7.1 Run `openspec validate add-claude-artifact-review --strict`.
- [x] 7.2 Run `openspec schema validate intent-driven` if schema guidance changed.
- [x] 7.3 Run all required helper checks from `test-plan.md`, including config validation, model/effort invalid config check, dry-run stage check, and fixture parser checks if implemented.
- [x] 7.4 Run `scripts/check-overlay` and report any limitations explicitly.
- [x] 7.5 Inspect `git status --short` and confirm no local secret files are tracked or staged.
- [x] 7.6 Fill the `test-plan.md` Evidence Log with RED/GREEN evidence or concise command-output references before marking implementation complete.
