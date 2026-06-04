## MODIFIED Requirements

### Requirement: Schema guidance includes persistent project context
The `intent-driven` schema SHALL instruct Codex to account for root `CONSTITUTION.md`, root `CONTEXT.md` or `CONTEXT-MAP.md`, root `ARCHITECTURE.md`, in-force ADR context, configured non-secret artifact review reports, and effective session-level workflow overlays during artifact creation, grill, test planning, verification, and apply guidance without making root project context files or session overlays OpenSpec change artifacts.

#### Scenario: Artifact instructions reference project context
- **GIVEN** Codex requests instructions for an intent-driven planning artifact
- **WHEN** OpenSpec returns the schema instruction text
- **THEN** the instruction tells Codex to consider relevant constitution, glossary, architecture, and ADR rules or decisions
- **AND** the instruction preserves the artifact's existing OpenSpec output path

#### Scenario: Apply instructions reference hardened context
- **GIVEN** an intent-driven change is apply-ready
- **WHEN** Codex requests `openspec instructions apply --change <change> --json`
- **THEN** the returned context files include proposal, specs, grill, design, design-review, ADR, test-plan, and tasks
- **AND** the apply instruction tells Codex to use the project-local `tdd` skill for behavior-changing implementation

#### Scenario: Review reports remain auxiliary context
- **GIVEN** Claude artifact review is enabled and has produced tracked review reports for a change
- **WHEN** Codex creates later artifacts, applies, or verifies the change
- **THEN** Codex treats those review reports as auxiliary reviewer context
- **AND** Codex does not treat the reports as replacements for proposal, specs, grill, design, design-review, ADR, test-plan, tasks, or OpenSpec validation

#### Scenario: Session overlays remain Codex-layer context
- **GIVEN** a session overlay configures automatic git checkpointing or Claude review settings
- **WHEN** Codex creates or consumes intent-driven artifacts
- **THEN** Codex uses the effective session overlay as workflow context
- **AND** Codex does not write the session overlay into OpenSpec lifecycle artifacts unless the artifact intentionally records a decision, override, or verification outcome
