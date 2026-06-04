# Changelog

All notable changes to Intent-Driven Codex are recorded here.

## v0.1.9 — 2026-06-04

### Added

- Artifact review now supports formal per-finding disposition records next to review reports, so reviewer findings can be marked `fixed`, `deferred`, `non_actionable`, or `user_blocked` before lifecycle progress.
- `scripts/openspec-artifact-review --disposition-summary` validates persisted review reports and disposition records locally without contacting the provider.
- Artifact review cost summaries now include display-ready RUB totals rounded upward to kopecks while preserving raw provider cost precision.

### Changed

- `/opsx:*` prompts and OpenSpec skills now require compact localized review status blocks wrapped in `---` separators instead of verbose English `Artifact Review` sections.
- Russian review status output uses localized labels such as `Ревью артефакта`, `Стоимость`, and `Замечания`, and avoids raw `mustFix` / `shouldFix` labels in chat.
- README, lifecycle docs, update-safety docs, architecture snapshot, and canonical artifact-review specs now describe formal dispositions and compact localized status output.

### Fixed

- Prevented silent lifecycle progress with unresolved artifact reviewer findings by adding provider-free disposition validation fixtures to `scripts/check-overlay`.

## v0.1.8 — 2026-06-03

### Added

- OpenAI-compatible artifact review now supports request-body provider routing via non-secret `requestProvider`, including Polza `provider.only` / `allow_fallbacks` routing for DeepSeek-only review calls.
- Live artifact review calls append an invocation cost ledger at `openspec/changes/<change>/reviews/artifact-review-costs.jsonl`, so `--cost-summary` reports total RUB cost across retries and re-runs instead of only the latest stage report.
- Review JSON output now includes compact `costSummary` metadata for user-facing total cost reporting after each reviewer call.

### Changed

- The default Polza review config uses `deepseek/deepseek-v4-pro` plus request-body routing to pin provider `DeepSeek` with fallbacks disabled.
- The default structured review mode is `json_object` for more reliable DeepSeek JSON responses on large artifact-review prompts.
- `/opsx:*` prompts and OpenSpec skills now require `## Artifact Review` blocks to show both current-call cost and total review cost for the change, including invocation counts and missing-cost counts.

### Fixed

- Fixed Polza `400 Модель ... не найдена` failures caused by putting provider routing parameters into the model alias.
- Fixed under-reported review spend when a stage was reviewed multiple times and the persisted stage report was overwritten.

## v0.1.7 — 2026-06-03

### Added

- Provider-neutral OpenAI-compatible artifact review is now the active optional reviewer path, with configurable provider URL, model, effort/reasoning policy, budgets, and structured cost-aware reports.
- Polza-compatible non-secret defaults are documented for `scripts/openspec-artifact-review` while real API keys remain local-only.

### Changed

- Claude-specific review invocation is disabled in the active intent-driven lifecycle while legacy Claude files remain for compatibility.
- README current-state, C4/Mermaid diagrams, ADR list, archive list, repository structure, and release metadata now reflect the OpenAI-compatible review architecture.
- The README hero image was regenerated to show Codex, OpenSpec, Review API, TDD slices, safe Git checkpoints, cost reports, and documentation-first workflow.

### Fixed

- Corrected stale README references to old `openspec-review*` helper/config names and incomplete post-archive state.
- Ensured the released README no longer describes OpenAI-compatible review as unpublished post-`v0.1.6` local state.

## v0.1.6 — 2026-06-02

### Changed

- Installed projects now receive the auto-repair tooling required by `scripts/check-overlay --no-smoke` through `scripts/install-overlay` and manifest-driven `opsx repair`.
- `scripts/test-auto-repair-tooling` now supports both source canonical docs and installed project doc aliases.
- The release documentation is now English-only: `README.ru.md` and the README language switch were removed.

### Fixed

- Fresh and Brownfield installed projects no longer need manual copies of `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, `scripts/test-auto-repair-tooling`, or canonical docs to pass `scripts/check-overlay --no-smoke`.
- Install, repair, and overlay diagnostics now warn when a target directory is nested inside a parent Git worktree but is not itself the Git root.

## v0.1.5 — 2026-06-02

### Added

- Source-owned auto-repair tooling: `bin/opsx`, `bin/openspec-shim`, and `manifest.yaml`.
- Regression suite `scripts/test-auto-repair-tooling` for install/shim/doctor/sanity behavior.
- `opsx sanity` and post-install checks for stale real OpenSpec binary paths after OpenSpec upgrades.

### Changed

- Updated release baseline to OpenSpec 1.4.0.
- `scripts/check-overlay` now validates auto-repair tooling regressions in addition to lifecycle smoke checks.

### Fixed

- `opsx install-auto-repair --source-root <root> --share-root <root>` now preserves the selected share/source root.
- The OpenSpec shim no longer runs auto-repair for help/version/no-op commands such as `openspec update --help` or `openspec --version`.
- `opsx doctor` now handles source-repo canonical docs (`docs/lifecycle.md`, `docs/update-safety.md`) and installed project aliases (`docs/intent-driven-lifecycle.md`, `docs/intent-driven-update-safety.md`) without false drift.

## v0.1.4 — 2026-05-30

### Added

- Optional OpenAI-compatible provider artifact review through `scripts/openspec-artifact-review`, `.codex/openspec-artifact-review.json`, stage-specific model/effort settings, structured review reports, and safe disabled defaults.
- Session-scoped artifact review controls via `/opsx:review-status`, `/opsx:review-on`, `/opsx:review-off`, `/opsx:review-reset`, `/opsx:review-set`, and ignored `.codex/session/` state.
- Automatic safe local OpenSpec checkpoint commits through `scripts/openspec-git-checkpoint` when session git discipline is `auto`.
- artifact review cost reporting after reviewer calls and aggregate apply summaries when cost metadata is available.

### Changed

- Greenfield installation documentation now uses `scripts/install-overlay --force-overlay` after `openspec init` so generated placeholder overlay files are replaced intentionally.
- Manual installation documentation now lists artifact review, session-state, checkpoint helper files, tracked non-secret config, and required ignore rules.
- Repository `openspec/config.yaml` now matches the richer intent-driven configuration generated by `scripts/install-overlay`.
- README/README.ru current-state, command, archive, structure, and support-skill documentation now reflect the shipped overlay.

### Fixed

- Corrected stale README active-change/archive claims and outdated ADR/structure summaries.
- Corrected `script/check-overlay` documentation typo to `scripts/check-overlay`.
- Clarified ADR 0007 wording in architecture/ADR index docs and aligned lifecycle dependency docs.

## v0.1.3 — 2026-05-28

### Added

- Mandatory `grill.md`, `design-review.md`, and `test-plan.md` gates for the intent-driven lifecycle.
- Project-local Matt `grill-with-docs` and `tdd` skills for context-aware review and RED/GREEN/REFACTOR apply discipline.
