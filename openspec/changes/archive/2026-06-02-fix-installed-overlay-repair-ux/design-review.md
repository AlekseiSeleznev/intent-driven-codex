## Context Read

- `proposal.md`, delta specs, `grill.md`, and `design.md` for `fix-installed-overlay-repair-ux`.
- Root `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/README.md`, and in-force ADRs 0001, 0003, 0005, 0006, 0007.
- Implementation surfaces named by the design: `scripts/install-overlay`, `scripts/check-overlay`, `scripts/test-auto-repair-tooling`, `bin/opsx`, `bin/openspec-shim`, `manifest.yaml`.

## Design Summary

The design makes installed projects receive the same auto-repair tooling that the installed overlay checker validates, preserves installed documentation aliases, adapts helper tests to canonical-or-alias doc layouts, and adds non-blocking parent Git-root diagnostics in target-oriented commands.

## Question Loop

### Question 1: Could copying `bin/opsx`, `bin/openspec-shim`, and `manifest.yaml` into installed projects violate the project-owned context boundary?

**Recommended answer:** No. These are template-owned overlay files, not project-owned context. They should be listed in `templateOwnedPaths` and replaced only under template-owned repair semantics.

**Resolution:** The design keeps `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, and `adr/README.md` as create-only/preserved files. Auto-repair tooling belongs to the Codex overlay distribution boundary from ADR 0007 and does not conflict with ADR 0003.

### Question 2: Is it safer to split `scripts/check-overlay` into source and installed modes instead of installing the helper?

**Recommended answer:** Not for this patch. The helper exercises public `opsx`/shim behavior and can be made layout-neutral. Splitting modes is future-compatible if source-only regressions grow, but the immediate v0.1.5 gap is better fixed by installing expected assets and adapting doc layout assumptions.

**Resolution:** Keep one checker path for now. If future helper tests become truly source-only, guard those tests explicitly rather than weakening installed-project checks broadly.

### Question 3: Could parent Git-root warnings mutate or leak sensitive information?

**Recommended answer:** No if implemented as path-only diagnostics using `git rev-parse` and `.git` existence checks. They should not read `.secrets.local.env`, Git remotes, config, or environment variables beyond paths already used by the command.

**Resolution:** Design is acceptable. The warning must be non-blocking and must not change the root-selection behavior by itself.

## Design Findings

- The design satisfies the fresh installed and brownfield alias-doc scenarios because both install and repair surfaces will seed the required local checker/tooling files.
- The design preserves source repository coverage by keeping canonical docs and full checker execution in source.
- The design avoids OpenSpec package mutation and remains compatible with ADR 0001.
- The design does not require a durable architecture change; it refines distribution and diagnostics inside the existing overlay architecture.

## Document Updates Applied

- No artifact changes were required during design review.

## Document Updates Required Before Next Gate

- `adr.md` should record that no durable ADR is needed and list ADRs considered.
- `test-plan.md` must make TDD RED/GREEN evidence explicit for installer, manifest repair, doc alias helper, Git-root warning, no-op help, doctor, stale sanity, and source smoke checks.
- `tasks.md` must order test-first implementation before production code changes.

## ADR Candidates

- None. This is a patch-level distribution/diagnostic refinement within in-force architecture.

## Open Questions

None.
