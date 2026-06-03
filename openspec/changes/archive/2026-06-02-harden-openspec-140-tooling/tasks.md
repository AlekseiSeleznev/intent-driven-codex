## 1. Source tooling and manifest

- [x] 1.1 RED: Add the `source-layout` regression in `scripts/test-auto-repair-tooling` and record failure while tracked `bin/opsx`, `bin/openspec-shim`, or `manifest.yaml` are absent/incomplete.
- [x] 1.2 GREEN: Add tracked `bin/opsx`, `bin/openspec-shim`, and `manifest.yaml` as the source-owned auto-repair tooling surface.
- [x] 1.3 GREEN/REFACTOR: Make `opsx` load root `manifest.yaml` with legacy `.codex/codex-openspec-powers/manifest.yaml` fallback and pass `source-layout`.

## 2. Safe install behavior

- [x] 2.1 RED: Add the `same-root-install` regression proving `install-auto-repair --source-root <root> --share-root <root>` must preserve a sentinel source/share root.
- [x] 2.2 GREEN: Update `opsx install-auto-repair` copy logic to skip destructive share refresh when source and share roots resolve to the same directory.
- [x] 2.3 GREEN/REFACTOR: Preserve different-root refresh behavior, executable installation, and state recording while keeping writes inside requested temp/share/bin paths.

## 3. Shim no-op repair gating

- [x] 3.1 RED: Add the `shim-noop` regression for `openspec update --help`, `openspec --version`, and a mutating update/init fixture.
- [x] 3.2 GREEN: Update `bin/openspec-shim` command classification so help/version/no-op commands never run repair and mutating commands still can.
- [x] 3.3 GREEN/REFACTOR: Keep target path parsing and Codex/all tool detection compatible with existing init/update behavior.

## 4. Doctor docs and stale real OpenSpec checks

- [x] 4.1 RED: Add `doctor-doc-layouts` regression covering source canonical docs as healthy and installed alias docs as checked drift.
- [x] 4.2 GREEN: Update `opsx` repair/doctor action collection to map installed doc aliases to canonical source docs only when the source root uses canonical docs.
- [x] 4.3 RED: Add `stale-real-openspec` regression covering current, stale, missing, and self-shim real OpenSpec paths through a non-secret test state override.
- [x] 4.4 GREEN: Add `opsx sanity` and post-install sanity checks that compare recorded/PATH/shim delegated OpenSpec versions and fail clearly on stale paths.

## 5. Overlay integration and documentation

- [x] 5.1 Wire `scripts/test-auto-repair-tooling all` into `scripts/check-overlay` and add syntax checks for `bin/opsx` and `bin/openspec-shim`.
- [x] 5.2 Update docs/README/install guidance if needed so tracked tooling, install safety, and stale path checks are discoverable without changing secret rules.
- [x] 5.3 Run each required TDD slice command and update `test-plan.md` evidence log with RED/GREEN results.
- [x] 5.4 Run `openspec validate harden-openspec-140-tooling --strict`, `scripts/check-overlay --no-smoke`, and full `scripts/check-overlay`.
- [x] 5.5 Confirm `git status --short` has no unrelated dirty or secret files and prepare an archive/release readiness report without archiving/pushing.
