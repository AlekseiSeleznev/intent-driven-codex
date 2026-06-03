## 1. Intent-driven schema

- [x] 1.1 Create `openspec/schemas/intent-driven/` from upstream `intent-driven` schema and templates.
- [x] 1.2 Adapt `schema.yaml` so artifact order is `proposal -> specs -> design -> adr -> tasks`.
- [x] 1.3 Set `design.requires` to include both `proposal` and `specs`.
- [x] 1.4 Change ADR artifact output to per-change `adr.md` instead of `../../../adr/*.md`.
- [x] 1.5 Update spec template so each requirement includes normative `MUST` or `SHALL` text before `#### Scenario:` blocks.
- [x] 1.6 Update ADR template to be a per-change ADR review with optional durable ADR references.
- [x] 1.7 Add `openspec/config.yaml` with `schema: intent-driven` for this template repository.
- [x] 1.8 Run `openspec schema validate intent-driven` and fix any schema/template issues.

## 2. Codex OpenSpec workflow commands and skills

- [x] 2.1 Add `.codex/prompts/opsx-new.md`, `.codex/prompts/opsx-continue.md`, `.codex/prompts/opsx-propose.md`, and `.codex/prompts/opsx-ff.md` adapted for Codex and the intent-driven schema.
- [x] 2.2 Add `.codex/prompts/opsx-apply.md`, `.codex/prompts/opsx-verify.md`, `.codex/prompts/opsx-sync.md`, and `.codex/prompts/opsx-archive.md` with mandatory context reads and git gates.
- [x] 2.3 Add `.codex/prompts/opsx-check-overlay.md` using valid OpenSpec 1.3.1 commands.
- [x] 2.4 Add optional `.codex/prompts/opsx-bulk-apply.md` and `.codex/prompts/opsx-bulk-archive.md` or explicitly document bulk apply/archive as follow-up if deferred.
- [x] 2.5 Add missing OpenSpec lifecycle skills under `.codex/skills/`: new, continue, verify, sync, ff, onboard, git discipline, and optional bulk skills.
- [x] 2.6 Ensure command names and skill names match Codex conventions and do not reference OpenCode runtime.

## 3. Review, Gherkin, C4, and ADR support skills

- [x] 3.1 Add `grill-with-docs` skill and wire proposal/design review guidance to it instead of `grill-me`.
- [x] 3.2 Add `gherkin-authoring` skill and ensure specs guidance preserves OpenSpec Markdown source-of-truth.
- [x] 3.3 Add `c4-diagrams` skill and ensure design guidance requires C4-style diagrams for non-trivial boundaries or an explicit no-diagram rationale.
- [x] 3.4 Add `architectural-decision-records` skill and preserve append-only ADR supersession rules.
- [x] 3.5 Add or update `openspec-git-discipline` skill with mandatory checkpoints and hard gates.

## 4. ADR lifecycle implementation

- [x] 4.1 Create top-level `adr/` with README or placeholder guidance without inventing durable decisions.
- [x] 4.2 Ensure per-change `adr.md` template lists in-force ADRs, evaluated decisions, new durable ADRs, superseded ADRs, and no-ADR rationale.
- [x] 4.3 Ensure apply/tasks/verify skills explicitly read top-level `adr/*.md` and derive the in-force ADR set from supersession links.
- [x] 4.4 Decide whether this bootstrap change should create an initial durable ADR for the overlay architecture; if yes, create it as a separate explicit step before apply or as part of apply.
  - Bootstrap ADR decision: create `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md` before archive to record the project-level overlay architecture decision.

## 5. Git discipline gates

- [x] 5.1 Embed checkpoint checks in continue/propose/ff flows after each artifact is created.
- [x] 5.2 Embed hard gate in apply so uncommitted planning artifacts block implementation.
- [x] 5.3 Embed hard gate in archive so archive requires verified and integrated implementation state.
- [x] 5.4 Ensure all mutating git operations require explicit user approval.
- [x] 5.5 Document override behavior when the user intentionally bypasses a checkpoint.

## 6. Overlay update safety and smoke checks

- [x] 6.1 Implement `/opsx:check-overlay` prompt and/or `scripts/check-overlay` to run compatibility checks.
- [x] 6.2 Ensure overlay checks use `openspec list --json` or `openspec status --change <name> --json`, not bare `openspec status --json`.
- [x] 6.3 Add smoke workflow that creates a temporary intent-driven change and verifies status/instructions/apply context.
- [x] 6.4 Define and implement safe smoke cleanup policy.
- [x] 6.5 Report compatibility failures by layer: OpenSpec CLI, local schema, Codex command adapter, generated skill, or project artifact.

## 7. Installation and documentation

- [x] 7.1 Add README and installation instructions for Greenfield repositories.
- [x] 7.2 Add Brownfield installation guidance with ownership boundaries and no-overwrite behavior.
- [x] 7.3 Add or update docs explaining lifecycle behavior, optional skill invocation conditions, ADR lifecycle, git checkpoints, and update safety.
- [x] 7.4 Decide whether to provide an installer script, documented copy procedure, or both.
  - Installation decision: provide both documented manual copy guidance and a no-overwrite `scripts/install-overlay` helper.
- [x] 7.5 Keep `FUNCTIONALITY.md` aligned with the implemented behavior.

## 8. Verification before apply-ready and archive-ready

- [x] 8.1 Run `openspec validate implement-intent-driven-codex-template --type change --strict`.
- [x] 8.2 Run `openspec schema validate intent-driven`.
- [x] 8.3 Run `openspec schemas --json` and confirm `intent-driven` is project-local.
- [x] 8.4 Run an intent-driven smoke change through proposal, specs, design, adr, and tasks status transitions.
- [x] 8.5 Run `openspec instructions apply --change <smoke-change> --json` and confirm proposal/specs/design/adr/tasks context files are returned.
- [x] 8.6 Confirm top-level ADR files are explicitly read by apply/tasks/verify guidance even though OpenSpec tracks per-change `adr.md`.
- [x] 8.7 Run git status checks and verify planning artifacts are committed before starting apply.
