## 1. Schema and artifact templates

- [x] 1.1 Update `openspec/schemas/intent-driven/schema.yaml` to use `proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks`.
- [x] 1.2 Add `grill.md` and `design-review.md` templates.
- [x] 1.3 Harden `test-plan.md` template for vertical RED/GREEN/REFACTOR slices and remove default `None` placeholders.
- [x] 1.4 Update schema README and smoke expectations.

## 2. Skills and prompts

- [x] 2.1 Vendor the upstream Matt `grill-with-docs` skill and references with an OpenSpec adapter.
- [x] 2.2 Vendor the upstream Matt `tdd` skill and references with an OpenSpec adapter.
- [x] 2.3 Update `/opsx:*` prompts for automatic grill gates and mandatory TDD apply behavior.
- [x] 2.4 Update OpenSpec lifecycle skills for `grill`, `design-review`, and canonical TDD.

## 3. Project context, ADR, and docs

- [x] 3.1 Add root `CONTEXT.md` glossary and document `CONTEXT-MAP.md` support.
- [x] 3.2 Add ADR 0005 and update `adr/README.md` and `ARCHITECTURE.md`.
- [x] 3.3 Update README EN/RU, lifecycle docs, OpenSpec bridge docs, and AGENTS.md for v0.1.3.
- [x] 3.4 Update canonical specs via archive/sync so the repository describes the new behavior.

## 4. Verification and installation

- [x] 4.1 Update `scripts/check-overlay` for the hardened artifact graph and apply context.
- [x] 4.2 Update `scripts/install-overlay` to install `CONTEXT.md`, Matt skills, and new config guidance safely.
- [x] 4.3 Run schema/change/all validation and overlay smoke checks.
- [x] 4.4 Archive the change after verification.

## 5. Publish and global install

- [x] 5.1 Rewrite local main into one clean `v0.1.3` commit after archive.
- [x] 5.2 Force-publish main, recreate tag/release `v0.1.3`, and verify GitHub state.
- [x] 5.3 Install the updated overlay into the global Codex OpenSpec locations.
