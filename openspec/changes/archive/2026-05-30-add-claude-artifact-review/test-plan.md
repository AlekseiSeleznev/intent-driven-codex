## TDD Strategy

Use the project-local `tdd` skill with vertical command-based slices. The public interface for this change is the overlay behavior exposed through `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, `/opsx:*` prompt/skill guidance, OpenSpec validation, and `scripts/check-overlay`.

Because this change adds local workflow tooling and documentation rather than an application library, the first RED checks are shell/CLI checks that fail before the helper/config/prompt updates exist. Apply must run one slice at a time:

1. create or identify the smallest command/check for one behavior;
2. observe the expected RED failure;
3. implement the minimal helper/config/prompt/docs change;
4. observe GREEN;
5. refactor only while GREEN;
6. record command output or a concise evidence reference here.

Automated tests must not make live Claude API/model calls by default. Use dry-run, fixture, and config-validation modes for automated evidence. A live `claude -p` smoke can be manual/opt-in only because it depends on local auth, budget, provider availability, and user consent.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| 1. Config presence and validation | `scripts/openspec-claude-review --validate-config` validates `.codex/openspec-claude-review.json` without calling Claude. | `scripts/openspec-claude-review --validate-config` fails because the helper/config do not exist yet. | Command exits `0` and reports disabled/safe config with balanced stage defaults. Also run `bash -n scripts/openspec-claude-review`. | Helper remains readable Bash with Python stdlib parsing only; no secret reads; no external Claude invocation in validation mode. |
| 2. Model/effort compatibility | Helper rejects or omits unsupported effort values according to stage policy. | Run helper validation against a temporary invalid config containing `claude-sonnet-4-6` + `xhigh`; expect non-zero strict validation before implementation. | Strict invalid config exits non-zero with a clear message; valid Opus `xhigh`, Sonnet `high`, and Haiku no-effort cases pass through dry-run output correctly. | Capability table is centralized; custom model override path is documented; no hard-coded provider secrets. |
| 3. Stage dry-run and input manifest | `scripts/openspec-claude-review --change <change> --stage specs --dry-run` resolves files and prints/writes a review plan without calling Claude. | Dry-run command fails or cannot find stage/context handling before implementation. | Dry-run exits `0` or configured skip code, lists selected files, model, effort, budget, output report path, and states no Claude call was made. | Stage file selection is deterministic and excludes `.secrets.local.env`, `.env.local`, local Claude transcripts/state, and unrelated large files. |
| 4. Structured report parsing and policy exit | Helper can process a fixture Claude result into `pass`, `warn`, `fail`, or `blocked` policy outcomes. | A fixture parse command or dry-run fixture mode fails before parser support exists. | Fixture `pass` exits `0`; fixture `fail` exits blocking status; invalid JSON exits configuration/parse status; reports include verdict, model, effort, reviewed files/hashes, findings, and omitted context. | Parser does not infer semantics from free-form prose; report schema version is explicit and stable. |
| 5. Workflow integration guidance | `/opsx:continue`, `/opsx:propose`, `/opsx:ff`, and `/opsx:verify` guidance accounts for configured Claude review. | `grep -R "openspec-claude-review" .codex/prompts .codex/skills` fails before prompt/skill updates. | Grep finds helper guidance in the required prompts/skills; wording preserves OpenSpec, grill, ADR, TDD, and git checkpoint gates. | Prompt changes are minimal, consistent, and do not require Claude when config is disabled. |
| 6. Overlay health and docs | `scripts/check-overlay` validates helper/config presence and dry-run behavior without a live Claude call; docs explain model/effort profiles. | `scripts/check-overlay --no-smoke` or targeted grep/docs checks fail before check/docs updates. | `scripts/check-overlay --no-smoke` passes; full `scripts/check-overlay` passes; docs mention enabling review, pinned IDs vs aliases, effort support, budgets, disabled default, and no secret values. | Check remains safe for missing Claude auth and does not patch installed OpenSpec packages. |

## Mocking / Boundary Policy

- Do not mock internal helper functions. Test behavior through the script command-line interface and tracked prompt/skill files.
- Mock only the external Claude boundary by using dry-run and fixture parser modes. Do not call a live Claude model in automated checks.
- Use temporary config files for invalid model/effort cases; do not edit `.secrets.local.env` and do not require local Claude auth.
- File-system assertions may inspect generated reports, selected file manifests, and grep results because those are public workflow outputs.

## Required Checks

Before marking apply complete:

- `openspec validate add-claude-artifact-review --strict`
- `bash -n scripts/openspec-claude-review`
- `scripts/openspec-claude-review --validate-config`
- At least one invalid model/effort validation check that fails as expected
- At least one valid dry-run stage check, e.g. `scripts/openspec-claude-review --change add-claude-artifact-review --stage specs --dry-run`
- Fixture/report parser checks for pass, fail, and invalid JSON outcomes if parser mode is implemented
- `grep -R "openspec-claude-review" .codex/prompts .codex/skills`
- `openspec schema validate intent-driven` if schema guidance changes
- `scripts/check-overlay`
- `git status --short` and tracked-secret check confirming no local secret env files are staged or tracked

Before archive:

- Re-run `openspec validate add-claude-artifact-review --strict`
- Re-run `scripts/check-overlay`
- Confirm tasks are checked complete with RED/GREEN evidence or documented exceptions
- Confirm `ARCHITECTURE.md` and `adr/README.md` include ADR 0006 if implementation accepts the architecture update tasks

## Evidence Log

- Slice 1 RED: `scripts/openspec-claude-review --validate-config` exited `127` before helper creation: `No such file or directory`.
- Slice 1 GREEN: `bash -n scripts/openspec-claude-review` passed; `scripts/openspec-claude-review --validate-config` exited `0` with `.codex/openspec-claude-review.json` and no config errors.
- Slice 2 GREEN/negative: temporary invalid config with `claude-sonnet-4-6` + `xhigh` exited `2` with unsupported-effort error; temporary Haiku effort config exited `0` and warned that effort is omitted.
- Slice 3 GREEN: temporary enabled dry-run config with `scripts/openspec-claude-review --change add-claude-artifact-review --stage specs --dry-run` exited `0`, reported selected proposal/spec/canonical spec files, effective effort, and report path without calling Claude.
- Slice 4 GREEN/negative: fixture parse mode exited `0` for `pass`, `1` for `fail`, and `2` for invalid JSON/missing verdict, producing structured verdict output without free-form inference.
- Slice 5 RED: `git grep -n "openspec-claude-review" HEAD -- .codex/prompts .codex/skills` exited `1` before prompt/skill integration in the helper-foundation commit.
- Slice 5 GREEN: `grep -R "openspec-claude-review" .codex/prompts .codex/skills` exited `0` with 8 prompt/skill references.
- Slice 6 GREEN: `scripts/check-overlay --no-smoke` passed, including helper presence, `bash -n`, config validation, schema validation, and no live Claude call.
- Slice 6 GREEN/full: `scripts/check-overlay` passed, including smoke change creation, dry-run Claude review against the smoke `specs` stage without contacting Claude, and apply-context verification.
- Final verification: `openspec validate add-claude-artifact-review --strict`, `openspec schema validate intent-driven`, `scripts/openspec-claude-review --validate-config`, and `bash -n scripts/openspec-claude-review` passed.
- Final secret check: `git ls-files -- '.secrets.local.env' '.secrets.*.local.env' '.env.local' '.env.*.local'` returned no tracked local secret files.

## TDD Exceptions

- **No automated live Claude model call.** Live `claude -p` execution depends on local Claude authentication, network/provider availability, and budget. Automated evidence must use `--validate-config`, `--dry-run`, and fixture parsing instead. A live review smoke is optional and requires explicit user approval at apply/verify time.
