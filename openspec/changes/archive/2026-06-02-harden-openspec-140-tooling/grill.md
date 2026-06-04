## Context Read

- `openspec/changes/harden-openspec-140-tooling/proposal.md`
- `openspec/changes/harden-openspec-140-tooling/specs/overlay-update-safety/spec.md`
- `openspec/changes/harden-openspec-140-tooling/specs/template-installation/spec.md`
- `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/README.md`
- In-force ADRs: `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`, `adr/0003-formalize-project-context-entrypoints.md`, `adr/0005-adopt-matt-grill-and-tdd-gates.md`, `adr/0007-adopt-automatic-checkpoints-and-claude-session-controls.md`
- Canonical specs: `openspec/specs/overlay-update-safety/spec.md`, `openspec/specs/template-installation/spec.md`, `openspec/specs/codex-opsx-workflow/spec.md`
- Relevant code/docs: `scripts/install-overlay`, `scripts/check-overlay`, global installed `/home/as/.local/share/codex-openspec-powers/bin/opsx`, global installed `/home/as/.local/share/codex-openspec-powers/bin/openspec-shim`, global installed manifests under `/home/as/.local/share/codex-openspec-powers/`, `docs/lifecycle.md`, `docs/update-safety.md`, `INSTALL_CODEX.md`, `README.md`

## Plan Summary

- The scope is tooling safety after OpenSpec 1.4.0, not a change to OpenSpec itself or a patch to installed OpenSpec packages.
- The current source checkout tracks overlay prompts/skills/scripts/docs but does not track the globally installed `opsx`, `openspec-shim`, or auto-repair manifest/source bundle that exhibit the reported bugs.
- To make `opsx doctor --root <source-repo> --source-root <source-repo>` meaningful and releaseable, this change should bring the auto-repair tooling and manifest into the source repository as first-class template assets, then regenerate installed bundles from those tracked files.
- `scripts/check-overlay` should remain the public compatibility command and gain regression fixtures for shim/opsx behavior without contacting external systems or reading `.secrets.local.env`.
- The specs correctly separate update-safety behavior (shim/doctor) from installation behavior (same-root installer and real OpenSpec sanity checks).

## Question Loop

### Question 1: Should the auto-repair tooling become tracked source in this repository, or should this change patch only the currently installed global copy?

**Recommended answer:** Track the auto-repair tooling (`bin/opsx`, `bin/openspec-shim`, manifest/source-bundle generation inputs) in the repository and use the installed global copy only as migration input.

**Rationale:** The user asked to work in the source repository and to make `opsx doctor --root <source-repo> --source-root <source-repo>` correct. Patching only `/home/as/.local/share/...` would not be captured by OpenSpec, would not survive release, and would contradict ADR 0001's project-local overlay/update-safety boundary. Repository inspection shows the failing code exists only in the installed global share today, so the design must add a tracked source-of-truth for it.

**Resolution:** Adopt the recommended answer. No user question is required because the repository/source-of-truth constraint and reported doctor command answer the uncertainty.

### Question 2: Should `openspec update --help` be treated as mutating because the command name is `update`?

**Recommended answer:** No. Any command containing help/version flags, and top-level version/help commands, must be treated as no-op/read-only before auto-repair eligibility is evaluated.

**Rationale:** The observed failure is `openspec update --help`; users expect help/version commands to be safe diagnostics. Repair after successful mutating `update` remains allowed, but no-op command classification must happen before command-name classification.

**Resolution:** Adopt the recommended answer. The overlay-update-safety spec already records this behavior.

### Question 3: Should installed projects use canonical source doc names or installed alias names?

**Recommended answer:** Keep both layouts: source repo canonical docs stay `docs/lifecycle.md` and `docs/update-safety.md`; installed projects keep alias docs `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md` for collision avoidance.

**Rationale:** `scripts/install-overlay` currently maps canonical source docs to installed aliases. That is Brownfield-safe because target projects may already own generic lifecycle/update docs. The bug is a manifest/doctor context problem, not a documentation naming problem.

**Resolution:** Adopt the recommended answer. The overlay-update-safety spec already records source-vs-installed behavior.

### Question 4: Should stale real OpenSpec path detection require internet/package-manager lookups?

**Recommended answer:** No. Use local executable resolution and `--version` comparisons only.

**Rationale:** The target issue is stale NVM path in local state. A deterministic local check can compare the recorded real binary, the shim-delegated binary, and a PATH-resolved real OpenSpec with shim/bin directories excluded. Network/package-manager lookups would add unnecessary instability and external dependencies.

**Resolution:** Adopt the recommended answer. No external systems or secrets are needed.

## Resolved Terms

- **Source repository doc layout**: canonical tracked docs `docs/lifecycle.md` and `docs/update-safety.md`.
- **Installed project doc alias layout**: Brownfield-safe installed docs `docs/intent-driven-lifecycle.md` and `docs/intent-driven-update-safety.md`.
- **Real OpenSpec binary**: the non-shim OpenSpec executable that the installed shim delegates to.

No `CONTEXT.md` update is required because these are tooling implementation terms for this change, not durable project glossary concepts.

## Document Updates Applied

- No proposal/spec changes were required after the grill. The existing deltas already capture the resolved behavior.

## Document Updates Required Before Next Gate

- None.

## ADR Candidates

- No new durable ADR candidate. Tracking auto-repair tooling as repository source follows ADR 0001's existing project-local overlay decision and is not a new project-wide architecture direction.

## Open Questions

None.
