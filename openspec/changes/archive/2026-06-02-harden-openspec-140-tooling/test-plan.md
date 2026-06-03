## TDD Strategy

Use the project-local `tdd` skill with command-based public-interface checks. The main observable interface is the auto-repair tooling as invoked through `bin/opsx`, `bin/openspec-shim`, and `scripts/check-overlay`. Each slice adds one regression check first, observes RED, implements only the needed tooling behavior, then reruns the slice for GREEN before refactor.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| 1. Source-owned tooling exists | `scripts/test-auto-repair-tooling source-layout` verifies tracked `bin/opsx`, `bin/openspec-shim`, and root `manifest.yaml` are usable as source | `scripts/test-auto-repair-tooling source-layout` fails because source tooling/manifest are absent or not executable | Same command passes; `python3 bin/opsx doctor --root . --source-root .` treats source canonical docs as healthy | Keep source layout checks path-based and no-secret; do not require installed alias docs in source root |
| 2. Same-root install safety | `bin/opsx install-auto-repair --source-root <root> --share-root <root> --bin-dir <tmp>` preserves `<root>` | Regression fixture fails because the current copy path deletes the share/source root or cannot install from it | Fixture passes and sentinel files remain after install; shims are installed in the temp bin dir | No destructive writes outside temp dirs; same-root branch is explicit and logged |
| 3. Shim no-op commands | `bin/openspec-shim update --help` and `bin/openspec-shim --version` delegate without repair | Fixture with fake real OpenSpec and fake opsx fails because repair marker is written for help/version | Fixture passes; no repair marker appears for no-op commands, while mutating update/init repair remains covered | Command classifier is small, documented, and tested for both top-level and subcommand help/version |
| 4. Source vs installed docs doctor | `bin/opsx doctor` supports source canonical docs and installed aliases | Fixture fails because doctor reports missing `docs/intent-driven-*.md` in source root or misses alias drift in installed root | Fixture passes for source root and reports drift when installed alias docs differ | Keep only the two explicit doc alias mappings; do not create broad fuzzy path matching |
| 5. Stale real OpenSpec path sanity | `bin/opsx sanity` / install post-check detects stale real OpenSpec path | Fixture with fake stale/current real binaries fails because stale path is accepted or no sanity command exists | Fixture passes: current versions match, stale/missing real path fails with clear diagnostic | Normalize version output conservatively and print only non-secret paths/versions |
| 6. Overlay integration | `scripts/check-overlay` runs the focused regression suite plus existing smoke checks | `scripts/check-overlay --no-smoke` fails before wiring/syntax if new tooling checks are absent or failing | `scripts/check-overlay` passes with regression suite and full smoke | Keep check-overlay deterministic; no live Claude, network, package-manager, or `.secrets.local.env` access |

## Mocking / Boundary Policy

- Use fake executable scripts only for external process boundaries: real OpenSpec CLI and installed `opsx` calls from the shim.
- Do not mock internal functions; run the public CLI scripts through subprocesses.
- All fixtures run in temporary directories and may set non-secret environment overrides such as `OPSX_STATE_FILE`, `OPSX_SHARE_ROOT`, `OPSX_REAL_OPENSPEC`, and `PATH`.
- Never read `.secrets.local.env`; no external systems are required.

## Required Checks

- `scripts/test-auto-repair-tooling source-layout`
- `scripts/test-auto-repair-tooling same-root-install`
- `scripts/test-auto-repair-tooling shim-noop`
- `scripts/test-auto-repair-tooling doctor-doc-layouts`
- `scripts/test-auto-repair-tooling stale-real-openspec`
- `scripts/test-auto-repair-tooling all`
- `openspec validate harden-openspec-140-tooling --strict`
- `scripts/check-overlay --no-smoke`
- `scripts/check-overlay`

## Evidence Log

- Slice 1 RED: `scripts/test-auto-repair-tooling source-layout` failed with `✗ bin/opsx missing or not executable` before tracked source tooling was added.
- Slice 1 GREEN: `scripts/test-auto-repair-tooling source-layout` passed after adding tracked `bin/opsx`, `bin/openspec-shim`, root `manifest.yaml`, root/legacy manifest loading, and source canonical doc mapping.
- Slice 2 RED: `scripts/test-auto-repair-tooling same-root-install` failed with `FileNotFoundError` after the old `copy_source_tree` deleted the source/share root before `copytree`.
- Slice 2 GREEN: `scripts/test-auto-repair-tooling same-root-install` passed after same-root install began preserving the source/share root and still installing temp shims.
- Slice 3 RED: `scripts/test-auto-repair-tooling shim-noop` failed with `✗ shim repaired for update --help` before no-op classification.
- Slice 3 GREEN: `scripts/test-auto-repair-tooling shim-noop` passed after `bin/openspec-shim` skipped help/version repair while preserving mutating update repair.
- Slice 4 RED: source doc alias failure was covered by Slice 1's initial source-layout failure; the focused `doctor-doc-layouts` fixture was then added to lock source canonical docs and installed aliases.
- Slice 4 GREEN: `scripts/test-auto-repair-tooling doctor-doc-layouts` passed, including source canonical docs healthy, installed alias docs healthy, and alias drift reported.
- Slice 5 RED: `scripts/test-auto-repair-tooling stale-real-openspec` failed because `opsx sanity` did not exist (`invalid choice: 'sanity'`).
- Slice 5 GREEN: `scripts/test-auto-repair-tooling stale-real-openspec` passed after adding `opsx sanity`, state-file override, version normalization, stale/missing/self-shim diagnostics, and post-install sanity.
- Slice 6 GREEN: `scripts/test-auto-repair-tooling all` passed with all focused regressions.
- Slice 6 GREEN: `scripts/check-overlay --no-smoke` passed after wiring the regression suite into the overlay checker.
- Final verification GREEN: individual required slice commands, `scripts/test-auto-repair-tooling all`, `openspec validate harden-openspec-140-tooling --strict`, `scripts/check-overlay --no-smoke`, and full `scripts/check-overlay` all passed on June 2, 2026.
- Additional sanity GREEN: `python3 bin/opsx doctor --root . --source-root .` reported `overlay healthy`; `python3 bin/opsx sanity --bin-dir /home/as/.local/bin` with the current global state reported OpenSpec version `1.4.0` for recorded/current paths.

## TDD Exceptions

None.
