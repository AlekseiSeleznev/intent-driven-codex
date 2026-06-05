## ADDED Requirements

### Requirement: Artifact review applies session config by default
The artifact review helper MUST use session-effective artifact review configuration for ordinary runs when no explicit config file is provided.

#### Scenario: Session review on enables ordinary dry-run review
- **GIVEN** tracked `.codex/openspec-artifact-review.json` has top-level `enabled: false`
- **AND** the `specs` stage is enabled in tracked stage policy
- **AND** the current session state has `artifactReview.decision=enabled`
- **WHEN** `scripts/openspec-artifact-review --change some-change --stage specs --dry-run` runs without `--config`
- **THEN** the helper merges tracked config with session state
- **AND** the stage is treated as enabled
- **AND** the dry-run result is not `skipped`

#### Scenario: Session review off controls ordinary dry-run skip
- **GIVEN** tracked artifact review config enables the `specs` stage
- **AND** the current session state has `artifactReview.decision=disabled`
- **WHEN** `scripts/openspec-artifact-review --change some-change --stage specs --dry-run` runs without `--config`
- **THEN** the helper uses the disabled session-effective config
- **AND** the result is a controlled `skipped` review

#### Scenario: Unset session preserves tracked config
- **GIVEN** the current session state has `artifactReview.decision=unset` or no session file exists
- **WHEN** artifact review runs without `--config`
- **THEN** tracked `.codex/openspec-artifact-review.json` remains the source of the top-level enabled decision

### Requirement: Explicit artifact review config has priority
The artifact review helper MUST NOT implicitly merge session state when the user passes an explicit `--config <path>`.

#### Scenario: Explicit disabled config ignores enabled session
- **GIVEN** session state has `artifactReview.decision=enabled`
- **AND** a separate config file has top-level `enabled: false`
- **WHEN** `scripts/openspec-artifact-review --config path/to/raw-disabled.json --change some-change --stage specs --dry-run` runs
- **THEN** the helper uses the explicit config exactly
- **AND** the result follows the explicit config rather than the session state

### Requirement: Raw config diagnostics are available
The artifact review helper MUST provide an explicit raw-config escape hatch for diagnostics.

#### Scenario: Raw-config flag bypasses session merge
- **GIVEN** tracked config has top-level `enabled: false`
- **AND** session state has `artifactReview.decision=enabled`
- **WHEN** artifact review runs without `--config` but with `--raw-config` or `--ignore-session`
- **THEN** the helper reads raw tracked config only
- **AND** the result is `skipped`

### Requirement: Session-effective artifact review remains secret-safe and provider-neutral
Session config merging SHALL remain provider-neutral and SHALL NOT reveal secret values.

#### Scenario: Config report only shows API key presence
- **GIVEN** the provider config names an API key environment variable
- **WHEN** artifact review validates or reports effective config
- **THEN** reports may include whether the environment variable is present
- **AND** reports do not include API key, token, password, or credential values
