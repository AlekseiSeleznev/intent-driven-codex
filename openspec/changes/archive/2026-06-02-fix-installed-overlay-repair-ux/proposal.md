## Why

After the v0.1.5 release, installed projects can receive the intent-driven overlay through normal OpenSpec update/repair paths but still fail `scripts/check-overlay --no-smoke` unless auto-repair tooling and canonical documentation are copied manually. This undermines the intended install/update UX and makes fresh or brownfield installed projects depend on undocumented post-install repairs.

## What Changes

- Make normal install/update/repair paths install every overlay-owned asset required for installed-project `scripts/check-overlay --no-smoke`, including auto-repair tooling and source regression helper coverage where appropriate.
- Align documentation file handling so installed alias docs and source canonical docs both satisfy repair tooling, doctor diagnostics, and overlay checks without manual canonical doc copies.
- Clarify source-repository versus installed-project overlay check responsibilities so installed projects are not forced to carry source-only regression assets unless the check requires them.
- Add a non-blocking diagnostic when a target directory is inside a parent Git worktree but is not itself a Git root, so users understand where Git status/ignore checks are being resolved.
- Preserve existing no-op/help behavior, `opsx doctor`, stale real OpenSpec sanity detection, and source-repository full overlay checks.

## Capabilities

- Modify `template-installation` to require installed projects to become overlay-check clean after ordinary install/update/repair without manual file copies.
- Modify `overlay-update-safety` to require source and installed documentation layouts, source-only regression checks, no-op shim behavior, and parent Git-root diagnostics to remain safe and observable.

## Impact

- Affected files are expected under `scripts/`, `bin/`, `manifest.yaml`, install/update docs, and OpenSpec specs for `template-installation` and `overlay-update-safety`.
- The implementation must keep OpenSpec as an external public CLI dependency and must not patch installed OpenSpec packages.
- Project-owned context (`CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/`) and local secrets remain preserved; `.secrets.local.env` must not be read, printed, copied, staged, or committed.
- Verification must include fresh installed-project, brownfield alias-doc, source `--no-smoke`, source full smoke, no-op help, source/installed `opsx doctor`, and stale-real-OpenSpec sanity regressions.
