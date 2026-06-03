## Tasks

### Planning and ADR

- [x] Create durable ADR `adr/0008-adopt-openai-compatible-artifact-review.md` superseding Claude-specific parts of ADR 0006/0007.
- [x] Update `ARCHITECTURE.md` to show provider-neutral OpenAI-compatible review and legacy-disabled Claude assets.
- [x] Update `adr/README.md` with ADR 0008 and supersession notes.

### Reviewer configuration and helper

- [x] Add `.codex/openspec-artifact-review.json` with safe disabled defaults for Polza/OpenAI-compatible review.
- [x] Add `scripts/openspec-artifact-review` with config validation, bundle collection, dry-run, parse-file, provider call, report writing, budget/cost handling, and cost-summary behavior.
- [x] Preserve deny patterns so `.secrets.local.env` and secret-looking files are never included in review prompts or reports.
- [x] Keep `scripts/openspec-claude-review` in the repository but remove it from active lifecycle call paths.

### Session controls and prompts

- [x] Extend `scripts/openspec-session-state` with provider-neutral `artifactReview` state, `--review`, `--set-review-stage`, and `--effective-review-config` behavior.
- [x] Add provider-neutral `/opsx:review-status`, `/opsx:review-on`, `/opsx:review-off`, `/opsx:review-reset`, and `/opsx:review-set` prompts.
- [x] Update active `/opsx:new`, `/opsx:continue`, `/opsx:propose`, `/opsx:ff`, `/opsx:apply`, `/opsx:verify`, and lifecycle skills to use artifact review and stop instructing Claude review calls.
- [x] Mark legacy `/opsx:claude-review-*` prompts as disabled/deprecated or remove them from primary documentation without deleting legacy files.

### Documentation, specs, and overlay checks

- [x] Update README feature bullets, command tables, optional review section, file tree, and diagrams to remove active Claude review wording.
- [x] Replace README/architecture Claude diagram node/icon with neutral `🔍 Artifact Reviewer` / OpenAI-compatible provider labeling.
- [x] Update canonical specs after archive or implementation scope as needed for `artifact-review`, `claude-artifact-review`, `intent-driven-schema`, and `template-installation`.
- [x] Update `scripts/check-overlay` checks from Claude-review markers to artifact-review markers while allowing legacy Claude files to remain.

### Local Polza setup and verification

- [x] Store local Polza settings in ignored `.secrets.local.env` using variable names such as `POLZA_AI_API_KEY`, `OPSX_REVIEW_BASE_URL`, and `OPSX_REVIEW_MODEL`; never stage or print values.
- [x] Run local dry-run and parse-file tests.
- [x] Run live Polza smoke review with `deepseek/deepseek-v4-pro` if credentials are locally available.
- [x] Run OpenSpec validation, session-state validation, check-overlay, git secret checks, and final `git status --short`.


### Evidence

- ADR and architecture updated: `adr/0008-adopt-openai-compatible-artifact-review.md`, `ARCHITECTURE.md`, and `adr/README.md`.
- Active review path replaced with `.codex/openspec-artifact-review.json`, `scripts/openspec-artifact-review`, provider-neutral `/opsx:review-*` prompts, and provider-neutral `artifactReview` session state in `scripts/openspec-session-state`; legacy Claude files remain present but inactive.
- Documentation and visuals updated: `README.md`, `INSTALL_CODEX.md`, `docs/lifecycle.md`, `docs/update-safety.md`, `docs/assets/intent-driven-codex-hero.png`, `openspec/README.md`, and schema docs no longer present Claude as active review.
- Local Polza settings stored only in ignored `.secrets.local.env`; tracked files contain only variable names/placeholders.
- Verification run on 2026-06-03: `openspec validate replace-claude-review-with-openai-compatible-review --strict`, `openspec schema validate intent-driven`, `scripts/openspec-session-state --validate`, helper config/dry-run/parse-file/missing-key tests, live Polza smoke with `deepseek/deepseek-v4-pro`, and full `scripts/check-overlay`.
- Live smoke initially returned a reviewer warning about constitution registration; resolved by adding the Polza external-system row with `POLZA_AI_API_KEY` variable name only to `CONSTITUTION.md`.
