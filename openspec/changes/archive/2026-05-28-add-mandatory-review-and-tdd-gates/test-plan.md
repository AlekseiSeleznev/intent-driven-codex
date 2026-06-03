## Verification Strategy

This change uses verification-first checks rather than classic unit-test-only TDD because it primarily changes OpenSpec schema files, Codex prompts/skills, shell scripts, and Markdown documentation.

The strongest executable checks are schema validation, OpenSpec change validation, temporary smoke-change lifecycle checks, apply-context inspection, overlay compatibility checks, and stale-text searches.

## Test-First Scope

- Run `openspec schema validate intent-driven` immediately after schema/template edits.
- Create a temporary smoke change and inspect `openspec status --change <smoke> --json` before considering prompt/script updates complete.
- Verify `openspec instructions review --change <smoke> --json` and `openspec instructions test-plan --change <smoke> --json` before marking artifact-template tasks complete.
- Complete smoke artifacts and inspect `openspec instructions apply --change <smoke> --json` for `review` and `test-plan` context before marking apply-context tasks complete.
- Run `scripts/check-overlay` after script updates.

## Requirement Mapping

| Requirement / Scenario | Verification method | Evidence expected | Timing |
| --- | --- | --- | --- |
| `intent-driven-schema`: expanded artifact lifecycle | `openspec status --change <smoke> --json` | Artifact order includes `proposal`, `specs`, `design`, `review`, `adr`, `test-plan`, `tasks` | Before apply completion |
| `intent-driven-schema`: review/test-plan templates | `openspec instructions review --change <smoke> --json`; `openspec instructions test-plan --change <smoke> --json` | Output paths `review.md` and `test-plan.md` with templates | Before apply completion |
| `context-aware-review`: mandatory review gate | Status smoke plus prompt/skill text search | `adr` remains blocked until `review.md`; prompts/skills mention mandatory review artifact | Before apply completion |
| `test-first-verification`: required test plan | Status smoke plus apply context check | `tasks` remains blocked until `test-plan.md`; apply context includes `test-plan` | Before apply completion |
| `codex-opsx-workflow`: apply/verify context | `openspec instructions apply --change <smoke> --json`; prompt/skill grep | `contextFiles.review` and `contextFiles.test-plan` exist; apply/verify guidance names them | Before apply completion |
| `git-discipline`: checkpoints include new gates | Prompt/skill grep | git discipline guidance names review/test-plan checkpoint boundaries | Before apply completion |
| `adr-lifecycle`: ADR follows review | Status smoke and schema inspection | `adr.requires` is `review`; ADR instructions read review findings | Before apply completion |
| `template-installation`: new projects get gates | `scripts/check-overlay`; installer source inspection | Smoke creates new artifacts; installer copies schema/templates/prompts/skills | Before archive |
| `overlay-update-safety`: checker validates expanded lifecycle | `scripts/check-overlay` | Checker passes and fails if review/test-plan context is missing | Before archive |

## Required Checks

- `openspec schema validate intent-driven`
- `openspec validate add-mandatory-review-and-tdd-gates --strict`
- `openspec validate --all --strict`
- Temporary smoke status/instructions/apply-context checks for `review` and `test-plan`
- `scripts/check-overlay`
- Stale wording search for old lifecycle references
- `git status --short` and local-secret tracked/ignored checks

## Limitations

- Classic red/green unit-test TDD is not a natural fit for Markdown prompt/skill/schema updates; this plan uses OpenSpec CLI smoke checks and overlay script checks as the verification-first substitute.
- Browser or external-system checks are not needed because this change does not access external systems or UI runtime services.
