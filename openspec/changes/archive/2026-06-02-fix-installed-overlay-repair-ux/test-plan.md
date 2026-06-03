## Test Strategy

Use command-based TDD slices because this change modifies Bash/Python overlay tooling. Each slice starts with an observable public command that fails on the current v0.1.5 behavior, then production/tooling changes are made to reach GREEN. Refactor only after the focused GREEN command passes.

## TDD Slices

### Slice 1: Direct installer seeds installed-project auto-repair tooling

**Behavior:** A target project installed through `scripts/install-overlay --force-overlay` receives the tooling required by `scripts/check-overlay --no-smoke`.

**RED command:** Create a temp target with `openspec init . --tools codex --profile core`, run source `scripts/install-overlay <target> --force-overlay`, then assert these paths exist and executable where applicable:

- `bin/opsx`
- `bin/openspec-shim`
- `manifest.yaml`
- `scripts/test-auto-repair-tooling`
- `scripts/install-overlay`

Expected RED before implementation: one or more paths are missing.

**GREEN command:** Same temp install check passes, followed by `scripts/check-overlay --no-smoke` inside the target.

### Slice 2: Manifest repair restores auto-repair tooling

**Behavior:** `opsx repair --yes --root <installed> --source-root <source>` restores missing template-owned auto-repair tooling.

**RED command:** Copy/install a target, remove `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, and `scripts/test-auto-repair-tooling`, then run `python3 <source>/bin/opsx repair --yes --root <target> --source-root <source>` and assert all files are restored.

Expected RED before implementation: repair does not restore files missing from `templateOwnedPaths`.

**GREEN command:** Repair restores the files, `python3 bin/opsx doctor --root <target> --source-root <source>` reports `overlay healthy`, and project-owned context is not overwritten.

### Slice 3: Auto-repair helper accepts installed doc aliases

**Behavior:** `scripts/test-auto-repair-tooling all` works in an installed-project layout that has alias docs but not canonical docs.

**RED command:** Prepare a temp installed project with `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md` only, plus installed auto-repair tooling, then run `scripts/test-auto-repair-tooling all`.

Expected RED before implementation: helper fails while trying to copy `docs/lifecycle.md` or `docs/update-safety.md` from the installed root.

**GREEN command:** The helper passes with alias docs and still passes in the source repository with canonical docs.

### Slice 4: Parent Git-root warning is observable and non-blocking

**Behavior:** Target-oriented tools warn when the target directory is inside a parent Git worktree but is not itself a Git root.

**RED command:** Create temp parent Git repo, create nested non-Git target directory, run dry-run install and/or `opsx doctor --root <nested> --source-root <source>`, and assert warning text mentions nested parent Git worktree.

Expected RED before implementation: no warning appears.

**GREEN command:** Warning appears for nested target, does not appear for target that is its own Git root, and commands still complete according to their normal success/failure conditions.

### Slice 5: No-op/help shim commands remain non-mutating

**Behavior:** `openspec update --help` and `openspec --version` delegate without invoking repair and without mutating project files.

**RED command:** Existing `scripts/test-auto-repair-tooling shim-noop` should already pass; if it fails after earlier changes, stop and fix before continuing.

Expected RED/GREEN classification: regression guard, expected GREEN before and after implementation.

**GREEN command:** `scripts/test-auto-repair-tooling shim-noop` passes, and a temp installed project running `openspec update --help` through the shim has unchanged file listing.

### Slice 6: Source and installed doctor/sanity checks stay green

**Behavior:** `opsx doctor` works for source and installed layouts, and `opsx sanity` still rejects stale, missing, or self-shim real OpenSpec paths.

**RED command:** Existing `scripts/test-auto-repair-tooling doctor-doc-layouts` and `stale-real-openspec` should pass before implementation; run them as regression guards before and after changes.

Expected RED/GREEN classification: regression guards, expected GREEN before and after implementation unless this change introduces a regression.

**GREEN command:** `scripts/test-auto-repair-tooling doctor-doc-layouts stale-real-openspec` equivalents pass; if the script only accepts one subcommand at a time, run them separately.

### Slice 7: Required installed and source overlay checks pass

**Behavior:** Installed and source overlay checks pass according to user-required acceptance criteria.

**RED command:** Before production changes, run a fresh temp installed project path and confirm `scripts/check-overlay --no-smoke` fails due to missing tooling and/or alias-doc helper behavior.

**GREEN commands:**

- Fresh temp installed project: `openspec init . --tools codex --profile core` + install/update/repair path -> `scripts/check-overlay --no-smoke` passes without manual copying.
- Existing/brownfield-style installed project with alias docs -> `scripts/check-overlay --no-smoke` passes.
- Source repo: `scripts/check-overlay --no-smoke` passes.
- Source repo: full `scripts/check-overlay` passes.

## Required Regression Commands

Run before verify and record outcomes:

```bash
openspec validate fix-installed-overlay-repair-ux --strict
scripts/test-auto-repair-tooling all
scripts/check-overlay --no-smoke
scripts/check-overlay
```

Plus temp-project checks for:

- fresh installed project after `openspec init . --tools codex --profile core` and documented install/update/repair path;
- brownfield/alias-doc installed project;
- no-op/help mutation guard for `openspec update --help`;
- `opsx doctor` source and installed layouts;
- `opsx sanity` stale real OpenSpec path detection.

## Evidence Log

Implementation must append RED/GREEN evidence here or to an apply report before marking tasks complete.

- Slice 1 RED: direct installer omitted `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, and `scripts/test-auto-repair-tooling` in temp install check.
- Slice 1 GREEN: direct installer seeded `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, `scripts/test-auto-repair-tooling`, and `scripts/install-overlay`; fresh installed `scripts/check-overlay --no-smoke` passed.
- Slice 2 RED: `python3 bin/opsx repair --yes --root <target> --source-root <source>` did not restore missing `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`, or `scripts/test-auto-repair-tooling` before manifest update.
- Slice 2 GREEN: repair restored the missing tooling from `manifest.yaml` `templateOwnedPaths`; installed doctor reported healthy.
- Slice 3 RED: alias-only helper layout failed before implementation because `make_minimal_source_root` tried to copy canonical docs from an installed root.
- Slice 3 GREEN: realistic installed alias-doc layout ran `scripts/test-auto-repair-tooling all` successfully without canonical docs.
- Slice 4 RED: exact parent Git-root diagnostic marker was absent before implementation.
- Slice 4 GREEN: `scripts/install-overlay --dry-run` warns with `inside parent Git worktree` for nested non-Git target and does not warn for a target that is its own Git root.
- Slice 5 regression GREEN: `scripts/test-auto-repair-tooling all` passed; separate fake-real-OpenSpec no-op guard showed `bin/openspec-shim update --help` did not invoke repair or mutate project files.
- Slice 6 regression GREEN: explicit `python3 bin/opsx doctor --root . --source-root .`, temp installed `opsx doctor`, and `scripts/test-auto-repair-tooling stale-real-openspec` passed.
- Slice 7 acceptance GREEN: fresh installed `scripts/check-overlay --no-smoke`, brownfield alias-doc installed `scripts/check-overlay --no-smoke`, source `scripts/check-overlay --no-smoke`, and source full `scripts/check-overlay` all passed.
