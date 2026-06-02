## TDD Strategy

Use command-based TDD slices because the change updates documentation, template config, release metadata, and lightweight validation checks. Each slice starts with an observable RED check against current files, then applies the smallest documentation/config/check update needed for GREEN. No external systems or secrets are needed, and `.secrets.local.env` must not be read.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| 1. Critical install instructions | Users following README/INSTALL Greenfield/manual paths get the correct installer flags and required overlay files | Grep/script check: README Greenfield command lacks `--force-overlay`; manual install docs omit `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, `scripts/openspec-session-state`, `scripts/openspec-git-checkpoint`, and `.codex/session/`; post-install `cp` block exists | Same check passes in `README.md`, `README.ru.md`, and `INSTALL_CODEX.md`; instructions say review/edit context files rather than overwrite | Keep installer as primary source; avoid duplicating behavior beyond explicit manual checklist |
| 2. README state and command docs | README/README.ru reflect current archive/ADR/command/skill state | Grep/script check finds stale `add-claude-artifact-review` active state, missing recent archive names, README ADR block missing ADR 0006/0007, missing `/opsx:claude-review-*` in command docs, and `Architecture diagrams` instead of `c4-diagrams` | Same checks pass; both README files agree on no active changes or command-derived state, archived changes/current ADRs, command discoverability, and skill naming | Prefer generated/directory references over brittle hard-coded long lists when readable |
| 3. Config/release metadata alignment | Repository config and release metadata match shipped template behavior | Diff/grep check shows `openspec/config.yaml` lacks installer `context`, `grill`, `design-review`, and `test-plan` rules; `VERSION` remains `0.1.3`; no `CHANGELOG.md` | Config includes installer-equivalent context/rules; `VERSION` and README badges/state use `0.1.4`; `CHANGELOG.md` records 2026-05-30 features/fixes | Keep config compatible with OpenSpec 1.3.x; do not introduce secrets |
| 4. Context docs polish | Context/lifecycle docs are coherent and use correct paths/dependencies | Grep check finds `script/check-overlay`, awkward ADR 0007 wording, and incomplete `design` dependency in lifecycle docs | Checks pass for `scripts/check-overlay`, readable ADR 0007 wording, and full design dependencies | Keep architecture snapshot content unchanged except wording fix |
| 5. Drift regression coverage and final verification | Overlay checks catch the fixed drift classes and full verification stays green | Targeted check for drift assertions is absent from `scripts/check-overlay`; full validation remains required | `scripts/check-overlay` includes lightweight drift checks and full verification commands pass | Checks must be deterministic and must not contact external systems |
| 6. Hero image asset inclusion | The updated documentation hero asset is intentionally included in this change | Git status/diff shows `docs/assets/intent-driven-codex-hero.png` changed outside artifact scope until user explicitly included it | Asset is committed as part of this change and referenced in task/evidence summaries | Do not regenerate or inspect secrets; treat as binary documentation asset |

## Mocking / Boundary Policy

- Do not mock internal scripts; use `grep`, small Python checks, OpenSpec CLI, and `scripts/check-overlay` through their public command interfaces.
- Do not contact Claude or any external service for this documentation/config cleanup.
- Do not read `.secrets.local.env`.
- For image asset inclusion, use git status/hash evidence rather than visual assertions unless a visual requirement emerges.

## Required Checks

- Targeted RED/GREEN checks for slices above.
- `openspec validate fix-validation-report-findings --strict`
- `openspec schema validate intent-driven`
- `openspec validate --all --strict`
- `scripts/check-overlay`
- `git diff --check`
- tracked-secret check: `git ls-files -- '.secrets.local.env' '.secrets.*.local.env' '.env.local' '.env.*.local'`
- `git status --short`

## Evidence Log

- Slice 1 RED: inline Python install-doc check failed as expected before edits: README/README.ru Greenfield commands lacked `--force-overlay`; `INSTALL_CODEX.md` manual procedure omitted `scripts/openspec-claude-review`, `.codex/openspec-claude-review.json`, `scripts/openspec-session-state`, `scripts/openspec-git-checkpoint`, and `.codex/session/`; unsafe blind `cp` commands remained.
- Slice 1 GREEN: the same inline Python install-doc check passed after updating `README.md`, `README.ru.md`, and `INSTALL_CODEX.md` to use `--force-overlay`, list required manual assets/ignore rules, and replace blind context copies with review/edit guidance.


- Slice 2 RED: inline Python README drift check failed as expected before edits: README state had stale active `add-claude-artifact-review`, recent 2026-05-30 archives were missing, Claude review session commands were not discoverable from the command table, repository structure missed session/checkpoint helpers and `CHANGELOG.md`, and the noncanonical `Architecture diagrams` wording remained.
- Slice 2 GREEN: the same README drift check passed after updating both README languages with command-derived active-state wording, all current archive entries, Claude review command rows, current structure entries, and canonical `c4-diagrams` skill naming.


- Slice 3 RED: inline Python config/release check failed as expected before edits: `openspec/config.yaml` lacked installer context/rules for `grill`, `design-review`, and `test-plan`; `VERSION` was `0.1.3`; both README files lacked `v0.1.4`; and `CHANGELOG.md` was absent.
- Slice 3 GREEN: the same config/release check passed after aligning `openspec/config.yaml` to installer markers, setting `VERSION` to `0.1.4`, updating README/README.ru release text and badges, and adding `CHANGELOG.md`.


- Slice 4 RED: inline Python context-doc check failed as expected before edits: `docs/update-safety.md` contained `script/check-overlay`, `ARCHITECTURE.md` and `adr/README.md` contained pasted ADR 0007 heading artifacts, and `docs/lifecycle.md` listed only `grill` as the `design` dependency.
- Slice 4 GREEN: the same context-doc check passed after correcting `scripts/check-overlay`, clarifying ADR 0007 wording, and listing `proposal, specs, grill` as `design` dependencies.


- Slice 5 RED: inline Python drift-check marker check failed as expected before edits because `scripts/check-overlay` lacked lightweight documentation/config drift assertions.
- Slice 5 GREEN: marker check, `bash -n scripts/check-overlay`, and `scripts/check-overlay --no-smoke` passed after adding deterministic drift checks for README install flags, Claude review commands, session/checkpoint helper docs, manual install helper mentions, `scripts/check-overlay` spelling, and tracked config markers.
- Slice 6 asset evidence: `git log --stat -- docs/assets/intent-driven-codex-hero.png` showed the hero asset was committed as part of this change; a subsequent binary refresh is included in the drift-check checkpoint so the working tree can return to clean before verification.


- Final verification GREEN: `openspec validate fix-validation-report-findings --strict`, `openspec schema validate intent-driven`, `openspec validate --all --strict`, full `scripts/check-overlay`, `git diff --check`, tracked-secret check, and `git status --short` all passed. `scripts/check-overlay` created and removed smoke change `zz-smoke-intent-overlay-20260530144359-952972` and verified Claude review dry-run/cost/budget fixtures without contacting live Claude.

## TDD Exceptions

None.
