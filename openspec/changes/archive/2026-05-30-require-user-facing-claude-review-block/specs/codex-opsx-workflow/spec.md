## ADDED Requirements

### Requirement: Opsx workflows render Claude review blocks
The `/opsx:*` lifecycle workflows that invoke Claude artifact review SHALL instruct Codex to render the mandatory `## Claude Review` block after each reviewer helper call and before checkpointing or continuing to dependent lifecycle work.

#### Scenario: Continue renders the review block
- **GIVEN** `/opsx:continue` creates a reviewable artifact
- **AND** the Claude artifact review helper is invoked or skipped for that stage
- **WHEN** Codex reaches the post-artifact checkpoint boundary
- **THEN** Codex renders `## Claude Review` with `Budget / cost`, `User-facing questions`, and `Short summary` sections
- **AND** Codex does not treat raw helper JSON output as sufficient disclosure

#### Scenario: Fast-forward and propose render review blocks per stage
- **GIVEN** `/opsx:ff` or `/opsx:propose` generates multiple artifacts in sequence
- **AND** Claude review is invoked or skipped for one or more stages
- **WHEN** each stage reaches its review boundary
- **THEN** Codex renders a separate `## Claude Review` block for that stage
- **AND** Codex stops the batch if the block contains a user-required question or unhandled blocker

#### Scenario: Apply renders review blocks and final aggregate cost
- **GIVEN** `/opsx:apply` invokes Claude artifact review during implementation or verification steps
- **WHEN** each reviewer call completes
- **THEN** Codex renders `## Claude Review` for the call before continuing
- **AND** the final apply summary still includes the aggregate Claude reviewer cost when available according to the existing apply cost-summary behavior
