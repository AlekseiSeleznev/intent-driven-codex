## Context Read

- `openspec/changes/fix-validation-report-findings/proposal.md`
- `openspec/changes/fix-validation-report-findings/validation-report.md`
- `openspec/changes/fix-validation-report-findings/specs/**/*.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `adr/*.md` summaries relevant to OpenSpec overlay, project context, grill/TDD, Claude review, and git checkpoints
- Existing docs/config referenced by the validation report: `README.md`, `README.ru.md`, `INSTALL_CODEX.md`, `docs/update-safety.md`, `docs/lifecycle.md`, `openspec/config.yaml`, `scripts/install-overlay`, `scripts/check-overlay`, `VERSION`

## Plan Summary

- The source validation report states the functional overlay is green, but documentation and install guidance drifted from current repository state.
- Two findings are critical because they can make installation fail: missing `--force-overlay` in Greenfield README instructions and incomplete manual copy instructions.
- The change groups 14 findings into documentation, install, config, version/changelog, and context-doc polish updates.
- The implementation must remain documentation/config focused, preserve the OpenSpec CLI boundary, and not read or expose local secrets.
- Verification must include OpenSpec validation, overlay checks, documentation consistency checks, tracked-secret checks, and git cleanliness.

## Question Loop

### Q1: Should this change split into five smaller changes as the validation report recommends, or proceed as one consolidated fix?

**Recommended answer:** Proceed as one consolidated fix.

**Rationale:** The user explicitly asked to move autonomously in fast-forward mode, and the findings are tightly coupled documentation/config drift from the same validation report. Splitting would multiply lifecycle overhead and leave critical install fixes delayed. The tasks can still be grouped by the five recommended seeds for reviewability.

**Resolution:** Proceed as one consolidated change, preserving the validation report's grouping inside design, test-plan, and tasks.

### Q2: Should version be bumped to `0.1.4`?

**Recommended answer:** Yes.

**Rationale:** The report identifies three post-`0.1.3` feature clusters (Claude artifact review, automatic checkpoint/session controls, and Claude review cost summary). This is more than a patch-level documentation fix; the public template now exposes new capabilities, commands, and helper scripts.

**Resolution:** Plan `VERSION=0.1.4`, README badge/state updates, and a new `CHANGELOG.md` entry.

### Q3: Should docs drift checks be implemented now or only documented?

**Recommended answer:** Implement lightweight deterministic checks now where practical, and document any deeper future checks.

**Rationale:** The project already uses `scripts/check-overlay` as a release gate. Adding cheap grep/state checks for known drift classes prevents recurrence without introducing external systems.

**Resolution:** Include a task to extend checks or add targeted validation commands for the fixed drift classes where practical.

## Resolved Terms

- `validation report findings`: findings F-01 through F-14 in `validation-report.md`, treated as the source backlog for this change.
- `documentation drift`: public documentation claims that no longer match repository state, installer behavior, or in-force ADR/spec state.
- No `CONTEXT.md` update is needed because these are workflow/documentation terms rather than stable domain language.

## Document Updates Applied

- Delta specs define expected behavior for installation docs, release docs, config alignment, architecture/ADR coherence, command docs, support-skill naming, and docs drift checks.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None. The change does not create a new durable architecture decision; it aligns documentation/config with existing ADRs 0001, 0003, 0005, 0006, and 0007.

## Open Questions

None.
