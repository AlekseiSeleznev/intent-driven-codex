## Context Reviewed

- `openspec/changes/add-mandatory-review-and-tdd-gates/proposal.md`
- `openspec/changes/add-mandatory-review-and-tdd-gates/specs/**/spec.md`
- `openspec/changes/add-mandatory-review-and-tdd-gates/design.md`
- `CONSTITUTION.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`
- `adr/0003-formalize-project-context-entrypoints.md`
- `adr/0004-adopt-mandatory-review-and-test-plan-gates.md`
- Current overlay files under `.codex/prompts`, `.codex/skills`, `openspec/schemas/intent-driven`, `scripts/`, and `docs/`.

## Plan Summary

- Expand the intent-driven schema lifecycle from `proposal -> specs -> design -> adr -> tasks` to `proposal -> specs -> design -> review -> adr -> test-plan -> tasks`.
- Make `review.md` the mandatory recorded `grill-with-docs` gate for development/enhancement changes.
- Make `test-plan.md` the mandatory TDD or verification-first planning gate before implementation tasks.
- Update prompts, skills, docs, architecture snapshot, ADR index, installer, and overlay smoke checks to match the new lifecycle.
- Preserve ADR 0001 and ADR 0003 boundaries: project-local overlay, public OpenSpec CLI usage, root project context outside OpenSpec change artifacts, and local-only secrets.

## Questions and Resolutions

- **Question:** Should existing active changes be auto-migrated to `review.md` and `test-plan.md`?
  - **Recommended answer:** Do not auto-migrate arbitrary target-project changes; document the breaking lifecycle and require explicit review/test-plan artifacts before apply.
  - **Resolution:** Accepted in design. This current change receives `review.md` and `test-plan.md` during apply because the schema implementation must validate itself after the lifecycle changes.
- **Question:** Should classic unit-test TDD be mandatory for every technology stack?
  - **Recommended answer:** Require classic TDD when feasible, otherwise require the strongest verification-first substitute and explicit limitations.
  - **Resolution:** Accepted in ADR 0004 and delta specs.

## Artifact Updates

- `openspec/schemas/intent-driven/schema.yaml` must add `review` and `test-plan` artifacts and update dependencies.
- `openspec/schemas/intent-driven/templates/review.md` and `test-plan.md` must be added.
- Prompts/skills must stop hard-coding the old lifecycle and must read/enforce `review.md` and `test-plan.md` via OpenSpec context files.
- `scripts/check-overlay` must validate the expanded lifecycle and apply context keys.
- `ARCHITECTURE.md` and `adr/README.md` must include ADR 0004 and the expanded current lifecycle after implementation.

## Remaining Risks

- This is a breaking lifecycle change for projects with active old-schema changes; documentation must make migration explicit.
- Prompt/skill drift is possible because many files mention lifecycle gates; verification must search for stale lifecycle wording.
- Some changes cannot use classic unit-test-first TDD; `test-plan.md` must make verification-first substitutes explicit instead of pretending TDD ran.
