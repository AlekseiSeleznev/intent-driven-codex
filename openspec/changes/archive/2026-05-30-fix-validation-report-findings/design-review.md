## Context Read

- `openspec/changes/fix-validation-report-findings/proposal.md`
- `openspec/changes/fix-validation-report-findings/validation-report.md`
- `openspec/changes/fix-validation-report-findings/specs/**/*.md`
- `openspec/changes/fix-validation-report-findings/grill.md`
- `openspec/changes/fix-validation-report-findings/design.md`
- `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, `adr/README.md`, in-force `adr/*.md`
- Existing target files referenced by validation findings and design

## Design Summary

- Consolidated documentation/config cleanup covering F-01 through F-14.
- Version bump to `0.1.4` with `CHANGELOG.md` is planned because recent features changed public template behavior.
- Installer is the primary installation path; manual docs should be explicit enough to pass overlay checks.
- `scripts/check-overlay` receives lightweight drift-regression checks but not brittle semantic README parsing.
- No new durable ADR is expected.

## Question Loop

### Q1: Could changing README state while an active change exists create a new contradiction?

**Recommended answer:** Yes, so README should describe the shipped/archive state generally and avoid listing this transient active change as a stable repository fact.

**Rationale:** This change itself is active during implementation. Updating README to say there are no active changes would be true only after archive. The old contradiction came from stale hand-maintained active-change text. The safest design is to state the intended release state after archive and/or phrase active change state as command-derived (`openspec list --json`) rather than fixed prose.

**Resolution:** Implementation should avoid embedding this active change as a long-term current-state entry; verify after archive readiness that public docs are not internally contradictory.

### Q2: Should full EN/RU README parity be enforced automatically now?

**Recommended answer:** No; enforce targeted factual checks and update both files together.

**Rationale:** Full bilingual semantic parity is brittle without a dedicated documentation checker. Targeted checks for the known drift classes are less fragile and sufficient for this change.

**Resolution:** Tasks should include targeted checks for both README files where findings mention both.

### Q3: Does the config alignment require changing installer behavior?

**Recommended answer:** No unless comparison reveals an installer bug.

**Rationale:** The validation finding says repository config is poorer than the installer-generated config. The installer output is the intended richer baseline. Aligning the repository file is enough unless tests expose mismatch.

**Resolution:** Update `openspec/config.yaml`; optionally add a check that key generated markers are present in both installer heredoc and tracked config.

## Design Findings

- **Finding:** README active-change state can be stale by nature.
  - **Resolution:** Prefer command-derived or release-state wording, not a fragile named active change.
- **Finding:** Manual install completeness is easy to regress because required helper files live across `.codex/`, `scripts/`, docs, and `.gitignore`.
  - **Resolution:** Add explicit manual list entries and lightweight overlay check assertions for required mentions.
- **Finding:** Version bump and CHANGELOG are release metadata, not architecture.
  - **Resolution:** Keep durable ADR unchanged; record no-ADR rationale.

## Document Updates Applied

- None before apply; design remains valid.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

None. This change aligns docs/config with accepted ADRs rather than creating a new durable decision.

## Open Questions

None.
