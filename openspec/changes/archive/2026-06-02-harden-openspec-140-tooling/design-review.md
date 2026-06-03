## Context Read

- `openspec/changes/harden-openspec-140-tooling/proposal.md`
- `openspec/changes/harden-openspec-140-tooling/specs/overlay-update-safety/spec.md`
- `openspec/changes/harden-openspec-140-tooling/specs/template-installation/spec.md`
- `openspec/changes/harden-openspec-140-tooling/grill.md`
- `openspec/changes/harden-openspec-140-tooling/design.md`
- `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/README.md`
- In-force ADRs 0001, 0003, 0005, 0007
- `scripts/check-overlay`, `scripts/install-overlay`, current global `bin/opsx` and `bin/openspec-shim`, global manifest/source layout, `docs/lifecycle.md`, `docs/update-safety.md`

## Design Summary

- The design adds tracked `bin/opsx`, `bin/openspec-shim`, and root `manifest.yaml` so auto-repair behavior is source-controlled and testable.
- It keeps installed documentation aliases while mapping canonical source docs to those aliases for repair/doctor comparisons.
- It makes same-root install preserve the selected share root and classifies help/version/no-op shim commands before repair eligibility.
- It adds local post-install real OpenSpec version/path sanity checks and a focused regression script wired into `scripts/check-overlay`.

## Question Loop

### Question 1: Does adding `bin/opsx` and `bin/openspec-shim` violate the project-local overlay / no OpenSpec patch rule?

**Recommended answer:** No, as long as they remain project-local helper scripts and install as a shim that delegates to the real OpenSpec CLI without modifying OpenSpec packages.

**Rationale:** ADR 0001 permits helper scripts and update checks, but forbids patching/forking OpenSpec. The design delegates to the real CLI and limits repair to manifest-owned overlay files.

**Resolution:** Adopt the recommended answer.

### Question 2: Is a root `manifest.yaml` too collision-prone for installed projects?

**Recommended answer:** It is acceptable in the source/share root but should not be installed into target projects by repair unless explicitly listed. Target project repair source discovery can read the share root manifest without copying it into target projects.

**Rationale:** The manifest is a distribution/source asset, not target project context. The design's `templateOwnedPaths` and `createOnlyPaths` should omit `manifest.yaml` for target repair.

**Resolution:** Adopt the recommended answer; implementation tasks should ensure repair does not create `manifest.yaml` in installed target roots.

## Design Findings

- **Finding: source-root discovery must be backward compatible.** Existing installed global shares use `.codex/codex-openspec-powers/manifest.yaml`; new source uses root `manifest.yaml`. The `opsx` loader must search both.
- **Finding: same-root install test must prove source survival.** The regression should place a sentinel file under the source/share root and assert it still exists after install.
- **Finding: shim no-op tests should avoid real project mutation.** Use fake real OpenSpec and fake `opsx` in a temp bin/share root; assert no repair marker file is written for `update --help` and `--version`.
- **Finding: stale path test should not modify global state.** Use `OPSX_STATE_FILE` or another test override so the fixture can point at fake current/stale real binaries without editing `/home/as/.local/share/.../state.json`.
- **Finding: check-overlay integration must remain no-secret.** The new regression script uses temp dirs and local fake executables only; it must not read `.secrets.local.env`.

## Document Updates Applied

- No design/spec changes were required. The findings refine task/test expectations and are captured here for ADR/test-plan/tasks.

## Document Updates Required Before Next Gate

- None.

## ADR Candidates

- No durable ADR needed. The design stays within ADR 0001's existing helper-script/update-safety model and does not supersede a durable architecture decision.

## Open Questions

None.
