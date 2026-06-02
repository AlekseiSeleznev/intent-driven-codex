## Tasks

- [x] 1. Add or update regression harness commands for installed overlay tooling
  - [x] 1.1 Add a temp installed-project check that demonstrates direct `scripts/install-overlay --force-overlay` currently omits required auto-repair tooling.
  - [x] 1.2 Add a temp repair check that demonstrates `opsx repair` currently does not restore missing auto-repair tooling from the manifest.
  - [x] 1.3 Add/adjust helper coverage for installed doc alias layouts without canonical docs.

- [x] 2. Install and repair missing template-owned auto-repair tooling
  - [x] 2.1 Update `scripts/install-overlay` to copy `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, `scripts/test-auto-repair-tooling`, and `scripts/install-overlay` as template-owned files with correct executable bits.
  - [x] 2.2 Update `manifest.yaml` `templateOwnedPaths` so `opsx repair` restores the same required tooling.
  - [x] 2.3 Verify direct install and manifest repair GREEN checks before marking this task complete.

- [x] 3. Make auto-repair helper layout-neutral for docs
  - [x] 3.1 Update `scripts/test-auto-repair-tooling` to source temporary docs from canonical paths or installed aliases.
  - [x] 3.2 Verify helper checks pass in source canonical and installed alias-doc layouts.

- [x] 4. Add parent Git-root warning diagnostics
  - [x] 4.1 Add non-blocking nested parent Git-root warning to `scripts/install-overlay`.
  - [x] 4.2 Add non-blocking nested parent Git-root warning to `bin/opsx` target commands.
  - [x] 4.3 Add non-blocking nested parent Git-root warning to `scripts/check-overlay` when relevant.
  - [x] 4.4 Verify warning appears for nested non-Git target and does not appear for target Git roots.

- [x] 5. Preserve no-op, doctor, and stale sanity behavior
  - [x] 5.1 Verify `openspec update --help` and `openspec --version` through the shim do not invoke repair or mutate project files.
  - [x] 5.2 Verify `opsx doctor` reports healthy for source and installed layouts.
  - [x] 5.3 Verify `opsx sanity` still fails on stale, missing, or self-shim real OpenSpec paths.

- [x] 6. Run required installed-project and source overlay acceptance checks
  - [x] 6.1 Fresh temp installed project: `openspec init . --tools codex --profile core` + install/update/repair path -> `scripts/check-overlay --no-smoke` passes without manual copying.
  - [x] 6.2 Brownfield/alias-doc installed project -> `scripts/check-overlay --no-smoke` passes.
  - [x] 6.3 Source repo `scripts/check-overlay --no-smoke` passes.
  - [x] 6.4 Source repo full `scripts/check-overlay` passes.
  - [x] 6.5 `openspec validate fix-installed-overlay-repair-ux --strict` passes.

- [x] 7. Record TDD evidence and final verification state
  - [x] 7.1 Update `test-plan.md` Evidence Log with RED/GREEN outcomes.
  - [x] 7.2 Ensure all tasks are checked only after corresponding evidence exists.
  - [x] 7.3 Run verify and report archive/release readiness separately from archive or release actions.
