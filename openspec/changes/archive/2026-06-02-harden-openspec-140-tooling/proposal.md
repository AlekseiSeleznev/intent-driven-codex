## Why

OpenSpec 1.4.0 compatibility verification found tooling regressions in the intent-driven auto-repair path that can mutate projects during no-op commands, report false drift in the source checkout, and silently keep a stale real OpenSpec binary path. The overlay needs deterministic safety checks before the next release so install/update flows remain conservative and diagnosable.

## What Changes

- Harden `opsx install-auto-repair` so installing from the same directory selected as the share root is a no-op/source-preserving operation rather than deleting the source before copy.
- Update the OpenSpec shim so help/version/no-op commands such as `openspec update --help` and `openspec --version` delegate to the real CLI without triggering auto-repair.
- Teach `opsx doctor`/repair drift checks to distinguish the source repository's canonical `docs/lifecycle.md` and `docs/update-safety.md` from installed project aliases `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md`.
- Add post-install sanity checks that verify the shim and real OpenSpec binary resolve to the same current OpenSpec version and fail clearly on stale paths.
- Add regression checks/tests covering same-root install safety, no-op shim behavior, source-vs-installed docs, stale real-binary detection, and the existing overlay checker.

## Capabilities

- Modified capability: `overlay-update-safety`
- Modified capability: `template-installation`

## Impact

- Affected tooling: `opsx` auto-repair/install/doctor code, OpenSpec shim, generated manifest/source bundle, and `scripts/check-overlay` regression coverage.
- Affected documentation/specs: OpenSpec deltas for update safety and template installation; release docs if they mention install/update checks.
- Project constraints: keep changes project-local; do not patch OpenSpec packages; do not read or print `.secrets.local.env`; use Bash/Python standard-library tooling and command-based TDD checks.
- Verification: run new targeted regression checks, `openspec validate harden-openspec-140-tooling --strict`, and `scripts/check-overlay` after overlay/template updates.
