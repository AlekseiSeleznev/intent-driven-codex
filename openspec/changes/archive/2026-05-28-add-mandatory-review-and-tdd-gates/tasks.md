## 1. Schema and artifact templates

- [x] 1.1 Update `openspec/schemas/intent-driven/schema.yaml` description and artifact graph to `proposal -> specs -> design -> review -> adr -> test-plan -> tasks`.
- [x] 1.2 Add a `review` artifact that generates `review.md`, requires `design`, and instructs Codex to use `grill-with-docs` context review before ADR planning.
- [x] 1.3 Add a `test-plan` artifact that generates `test-plan.md`, requires `adr`, and instructs Codex to map requirements/scenarios to TDD or verification-first checks before tasks.
- [x] 1.4 Change the `adr` artifact dependency from `design` to `review` and update ADR instructions to read review findings before durable-decision planning.
- [x] 1.5 Change the `tasks` artifact dependencies and instructions so tasks read `specs`, `design`, `review`, `adr`, and `test-plan` context before implementation planning.
- [x] 1.6 Update the `apply` instruction so `/opsx:apply` reads and enforces `review.md` and `test-plan.md` in addition to existing artifacts and project context.
- [x] 1.7 Add `openspec/schemas/intent-driven/templates/review.md` with sections for context reviewed, plan summary, questions/resolutions, artifact updates, and remaining risks.
- [x] 1.8 Add `openspec/schemas/intent-driven/templates/test-plan.md` with sections for verification strategy, test-first scope, requirement mapping, required checks, and limitations.
- [x] 1.9 Run `openspec schema validate intent-driven` and fix schema/template errors before continuing.

## 2. Codex prompts and lifecycle skills

- [x] 2.1 Update `.codex/prompts/opsx-new.md` guidance to keep stopping after scaffold while presenting the expanded lifecycle when relevant.
- [x] 2.2 Update `.codex/prompts/opsx-continue.md` so the next ready artifact can be `review` or `test-plan` and only one artifact is created per continue run.
- [x] 2.3 Update `.codex/prompts/opsx-propose.md` and `.codex/prompts/opsx-ff.md` so fast planning creates `review.md` and `test-plan.md` before `tasks.md`.
- [x] 2.4 Update `.codex/prompts/opsx-apply.md` and `.codex/prompts/opsx-bulk-apply.md` to block apply on missing or dirty review/test-plan artifacts and to include them in Goal handoff context.
- [x] 2.5 Update `.codex/prompts/opsx-verify.md` to report unresolved review findings and missing test-plan evidence as critical verification issues.
- [x] 2.6 Update `.codex/prompts/opsx-archive.md`, `.codex/prompts/opsx-sync-specs.md`, and `.codex/prompts/opsx-check-overlay.md` prose where they name the old lifecycle.
- [x] 2.7 Update `.codex/skills/openspec-new-change/SKILL.md`, `openspec-continue-change/SKILL.md`, `openspec-propose/SKILL.md`, and `openspec-ff-change/SKILL.md` to rely on OpenSpec status/instructions for the expanded artifact graph and to describe the new gates.
- [x] 2.8 Update `.codex/skills/openspec-apply-change/SKILL.md`, `openspec-bulk-apply-change/SKILL.md`, `openspec-verify-change/SKILL.md`, `openspec-archive-change/SKILL.md`, and `openspec-sync-specs/SKILL.md` to read/enforce review and test-plan context.
- [x] 2.9 Update `.codex/skills/grill-with-docs/SKILL.md` so its guidance explicitly supports writing `review.md` as the mandatory context-aware review artifact.
- [x] 2.10 Update `.codex/skills/gherkin-authoring/SKILL.md`, `.codex/skills/c4-diagrams/SKILL.md`, `.codex/skills/architectural-decision-records/SKILL.md`, and `.codex/skills/openspec-git-discipline/SKILL.md` only where their trigger/gate prose references the old lifecycle.

## 3. Overlay checks, installer, and generated project assets

- [x] 3.1 Update `scripts/check-overlay` expected artifact list from `proposal/specs/design/adr/tasks` to `proposal/specs/design/review/adr/test-plan/tasks`.
- [x] 3.2 Update the `scripts/check-overlay` smoke change to create valid temporary `review.md` and `test-plan.md` artifacts before `tasks.md`.
- [x] 3.3 Update `scripts/check-overlay` apply-context assertions so missing `review` or `test-plan` context fails the check.
- [x] 3.4 Update `scripts/install-overlay` so new schema templates, prompts, skills, docs, constitution, architecture, and ADR assets are copied consistently into target projects.
- [x] 3.5 Verify `.secrets.local.env` and other local secret files remain ignored/untracked and are not read or copied by installer/check scripts.

## 4. Documentation, architecture snapshot, and ADR index

- [x] 4.1 Update `README.md` with the expanded lifecycle, mandatory review gate, mandatory test-plan/TDD gate, and migration note for active v0.1.1 changes.
- [x] 4.2 Update `README.ru.md` with the same functionality and migration guidance in Russian.
- [x] 4.3 Update `AGENTS.md` so agent instructions require `review.md` and `test-plan.md` before implementation for development/enhancement changes.
- [x] 4.4 Update `INSTALL_CODEX.md` to describe that installed/new projects receive the expanded lifecycle and templates.
- [x] 4.5 Update `docs/lifecycle.md` to show the new artifact graph, runtime sequence, mandatory review/test-plan behavior, and verification evidence flow.
- [x] 4.6 Update `docs/update-safety.md` to include review/test-plan overlay smoke expectations and active-change migration cautions.
- [x] 4.7 Update `openspec/README.md` as the OpenSpec bridge so it indexes the expanded lifecycle while keeping root context outside change artifacts.
- [x] 4.8 Update `ARCHITECTURE.md` after schema/prompt implementation to show ADR 0004 and the expanded current lifecycle.
- [x] 4.9 Update `adr/README.md` to list ADR 0004 as in force without changing accepted ADR history.
- [x] 4.10 Update `VERSION` and release metadata only if the user asks to prepare/publish a release; otherwise leave release publication out of this apply run.

## 5. Verification-first implementation checks

- [x] 5.1 Before production edits are marked complete, run `openspec schema validate intent-driven` and keep the failing/passing output as the schema-level verification check.
- [x] 5.2 Create a temporary smoke change and verify `openspec status --change <smoke> --json` reports artifact order `proposal`, `specs`, `design`, `review`, `adr`, `test-plan`, `tasks`.
- [x] 5.3 Verify `openspec instructions review --change <smoke> --json` returns `review.md` output path and the review template.
- [x] 5.4 Verify `openspec instructions test-plan --change <smoke> --json` returns `test-plan.md` output path and the test-plan template.
- [x] 5.5 Complete the smoke change artifacts and verify `openspec instructions apply --change <smoke> --json` includes `review` and `test-plan` context files.
- [x] 5.6 Run `scripts/check-overlay` and fix any overlay compatibility failures.
- [x] 5.7 Run `openspec validate add-mandatory-review-and-tdd-gates --strict` and `openspec validate --all --strict` before verification/archive.
- [x] 5.8 Search project docs/prompts/skills/scripts for stale old-lifecycle wording and either update it or document why the reference is historical.
- [x] 5.9 Show `git status --short` and confirm no local secret files are staged or tracked before claiming apply completion.
