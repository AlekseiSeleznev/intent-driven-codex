## 1. Critical Installation Documentation

- [x] 1.1 RED: Add/run targeted checks proving README Greenfield docs omit `--force-overlay`, manual docs omit required overlay files, and post-install docs still show unsafe blind `cp` commands.
- [x] 1.2 GREEN: Update `README.md`, `README.ru.md`, and `INSTALL_CODEX.md` so Greenfield uses `--force-overlay`, manual copy instructions include all required helper/config/ignore assets, and context setup is review/edit guidance.

## 2. README State, Commands, and Skill Naming

- [x] 2.1 RED: Add/run targeted checks proving README state drift: stale active change, incomplete archive/ADR state, missing Claude review session commands in command docs, stale repository structure, and noncanonical `Architecture diagrams` skill name.
- [x] 2.2 GREEN: Update `README.md` and `README.ru.md` to match repository state, archived changes, in-force ADRs, command set, repository structure, and `c4-diagrams` naming.

## 3. Config and Release Metadata

- [x] 3.1 RED: Add/run targeted checks proving `openspec/config.yaml` lacks installer-equivalent context/rules, `VERSION` remains `0.1.3`, README version/state is stale, and `CHANGELOG.md` is missing.
- [x] 3.2 GREEN: Align `openspec/config.yaml` with installer-generated intent-driven context/rules, bump `VERSION` and README state to `0.1.4`, and add `CHANGELOG.md`.

## 4. Context Documentation Polish

- [x] 4.1 RED: Add/run targeted checks proving `script/check-overlay`, awkward ADR 0007 wording, and incomplete `design` dependencies remain.
- [x] 4.2 GREEN: Fix `docs/update-safety.md`, `ARCHITECTURE.md`, `adr/README.md`, and `docs/lifecycle.md` for those polish findings.

## 5. Drift Regression Checks

- [x] 5.1 GREEN: Extend `scripts/check-overlay` with lightweight documentation/config drift checks for corrected command paths, required manual-install helper mentions, Claude review command discoverability, and tracked config markers.
- [x] 5.2 GREEN: Ensure the included `docs/assets/intent-driven-codex-hero.png` binary asset remains staged/committed as part of this documentation change.

## 6. Verification and Evidence

- [x] 6.1 Run required OpenSpec checks: `openspec validate fix-validation-report-findings --strict`, `openspec schema validate intent-driven`, and `openspec validate --all --strict`.
- [x] 6.2 Run overlay/hygiene checks: `scripts/check-overlay`, `git diff --check`, tracked-secret check, and `git status --short`.
- [x] 6.3 Update `test-plan.md` evidence log and mark tasks complete only after matching RED/GREEN or verification evidence exists.
