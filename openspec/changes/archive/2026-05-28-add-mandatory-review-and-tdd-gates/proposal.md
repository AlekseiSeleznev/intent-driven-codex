## Why

Development and enhancement changes currently may skip context-aware review and do not have a required test-first verification plan before implementation. This lets ambiguity, weak verification strategy, and review findings remain in chat instead of being integrated into OpenSpec artifacts for new projects.

## What Changes

- **BREAKING**: Extend the `intent-driven` lifecycle from `proposal -> specs -> design -> adr -> tasks` to `proposal -> specs -> design -> review -> adr -> test-plan -> tasks`.
- Add a required `review.md` artifact that records mandatory `grill-with-docs` review for development/enhancement changes and tracks how review resolutions are integrated into proposal, specs, design, or ADR planning.
- Add a required `test-plan.md` artifact that defines TDD or the strongest feasible verification-first substitute before implementation tasks are written.
- Update `/opsx:*` prompts, OpenSpec lifecycle skills, schema instructions, templates, overlay smoke checks, docs, and canonical specs to enforce the new gates.
- Add a durable ADR for the lifecycle decision and update `ARCHITECTURE.md` during apply because the current workflow architecture changes.
- Keep dirty local tooling state classification out of this change; it will be handled by a separate follow-up change.

## Capabilities

- `intent-driven-schema` — modify lifecycle artifact order and schema guidance for `review` and `test-plan`.
- `context-aware-review` — modify review from conditional guidance to a mandatory development/enhancement gate and define artifact integration rules.
- `test-first-verification` — add a new capability for mandatory TDD/verification-first planning.
- `codex-opsx-workflow` — modify `/opsx:*`, apply, and verify behavior to read and enforce the new artifacts.
- `git-discipline` — modify checkpoint and apply-blocking rules to include `review.md` and `test-plan.md`.
- `adr-lifecycle` — modify ADR review ordering so ADR decisions are informed by `review.md`.
- `template-installation` — modify installation/smoke guidance so new projects receive the new gates.
- `overlay-update-safety` — modify overlay compatibility checks to validate the expanded lifecycle.

## Impact

- Affected schema files: `openspec/schemas/intent-driven/schema.yaml` and templates under `openspec/schemas/intent-driven/templates/`.
- Affected Codex assets: `.codex/prompts/opsx-*.md`, `.codex/skills/grill-with-docs/SKILL.md`, and relevant `.codex/skills/openspec-*` lifecycle skills.
- Affected checks/scripts: `scripts/check-overlay` and `scripts/install-overlay` source copying if new templates or docs are added.
- Affected documentation: `README.md`, `README.ru.md`, `AGENTS.md`, `INSTALL_CODEX.md`, `docs/lifecycle.md`, `docs/update-safety.md`, `openspec/README.md`, `CONSTITUTION.md`, `ARCHITECTURE.md`, and `adr/README.md`.
- Affected durable decisions: add a new top-level ADR that references ADR 0001 and ADR 0003 without superseding them; update `ARCHITECTURE.md` because the lifecycle architecture changes.
- Verification must include `openspec schema validate intent-driven`, `openspec validate --all --strict`, `scripts/check-overlay`, and a smoke check proving the new artifact order and apply context include `review` and `test-plan`.
- No external systems or local secret values are needed for this change.
