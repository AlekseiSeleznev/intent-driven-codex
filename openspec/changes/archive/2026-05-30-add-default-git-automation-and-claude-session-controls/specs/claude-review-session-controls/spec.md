## ADDED Requirements

### Requirement: Claude review session overlay is non-secret and reversible
The system MUST support a session-level Claude artifact review overlay that merges over `.codex/openspec-claude-review.json` for the current Codex/OpenSpec session, and the overlay SHALL contain only non-secret review policy fields.

#### Scenario: Session overlay inherits global defaults
- **GIVEN** `.codex/openspec-claude-review.json` defines global and per-stage Claude review defaults
- **AND** the current session overlay enables review without a `specs` stage model override
- **WHEN** Codex resolves the effective `specs` review configuration
- **THEN** Codex uses the project-global `specs` model, effort, budget, prompt profile, and blocking policy
- **AND** Codex applies the session enablement value over the global enablement value

#### Scenario: Session overlay overrides one stage
- **GIVEN** the project-global review config sets `design-review` to `claude-opus-4-8` with `high` effort
- **AND** the session overlay sets `design-review` effort to `xhigh`
- **WHEN** Codex resolves the effective `design-review` review configuration
- **THEN** Codex keeps the project-global model
- **AND** Codex uses the session overlay effort for that session only

#### Scenario: Session overlay does not store secrets
- **GIVEN** a user enables Claude review for the current session
- **WHEN** Codex records the session overlay
- **THEN** the overlay may contain booleans, stage names, model IDs or aliases, effort levels, budgets, prompt profiles, and blocking policies
- **AND** the overlay does not contain Claude credentials, tokens, private endpoints, or values from `.secrets.local.env`

#### Scenario: Session overlay can be cleared
- **GIVEN** a session overlay exists for Claude artifact review
- **WHEN** the user asks Codex to reset Claude review session settings
- **THEN** Codex clears the session overlay for the current session
- **AND** subsequent review resolution falls back to `.codex/openspec-claude-review.json`

### Requirement: Budget-exhausted Claude review disables session review
The system MUST detect Claude Code budget-exhaustion results and stop dependent lifecycle progression until the user has seen the blocker, while disabling Claude review for the current session to prevent repeated failing reviewer calls.

#### Scenario: Claude Code reports max budget exhausted
- **GIVEN** Claude review is enabled for the current session
- **AND** the effective review configuration includes a `maxBudgetUsd` cap
- **WHEN** `scripts/openspec-claude-review` receives a Claude Code result with subtype `error_max_budget_usd`
- **THEN** the review result is `blocked`
- **AND** the result reports that the configured budget was exhausted
- **AND** Codex records the current session Claude review decision as disabled
- **AND** dependent lifecycle steps stop until the user decides whether to raise or clear the budget cap and explicitly re-enable review

### Requirement: Session controls support stage-specific model and effort profiles
The system MUST let users configure Claude model and effort per lifecycle stage at session scope, including distinct Opus and Sonnet profiles, while reusing canonical Claude artifact review validation and fallback behavior.

#### Scenario: Sonnet profile is used for routine stages
- **GIVEN** the session overlay sets `specs` to `model: claude-sonnet-4-6` and `effort: medium`
- **WHEN** Codex runs Claude review for `specs`
- **THEN** the review command uses the Sonnet model and medium effort for that stage
- **AND** other stages continue to use their resolved global or session-specific settings

#### Scenario: Opus profile is used for architecture-sensitive stages
- **GIVEN** the session overlay sets `design-review` to `model: claude-opus-4-8` and `effort: xhigh`
- **WHEN** Codex runs Claude review for `design-review`
- **THEN** the review command uses the Opus model and xhigh effort for that stage
- **AND** the review report records the effective model and effort

#### Scenario: Invalid effort is rejected before review
- **GIVEN** the session overlay sets a Sonnet stage to unsupported `xhigh` effort in strict mode
- **WHEN** Codex validates the effective review configuration
- **THEN** Codex reports the model/effort compatibility error
- **AND** Codex does not call `claude -p` until the setting is corrected or explicitly overridden by policy

#### Scenario: Alias use is visible in session status
- **GIVEN** the session overlay sets `tasks` to model alias `sonnet`
- **WHEN** Codex shows Claude review session status
- **THEN** Codex reports that the effective model is a floating alias
- **AND** Codex explains that the resolved provider target may vary over time

### Requirement: Session review intent is prompted once per entry session
The system MUST ask for Claude review enablement at the start of a Codex/OpenSpec session or at key lifecycle entry points when no explicit session decision exists, and SHALL avoid repeated prompts after a decision is recorded.

#### Scenario: New workflow prompts for review enablement
- **GIVEN** no Claude review session decision exists
- **WHEN** the user starts a new OpenSpec change through `/opsx:new`
- **THEN** Codex asks whether to enable Claude artifact review for the session
- **AND** Codex records the answer as a session overlay decision

#### Scenario: Propose workflow prompts before multi-artifact generation
- **GIVEN** no Claude review session decision exists
- **WHEN** the user invokes `/opsx:propose` for a fast proposal workflow
- **THEN** Codex asks whether to enable Claude artifact review before generating artifacts that may be reviewed
- **AND** Codex applies the decision to all subsequent stages in that session

#### Scenario: Apply workflow reuses prior decision
- **GIVEN** the user already disabled Claude review for the current session
- **WHEN** the user invokes `/opsx:apply <change>`
- **THEN** Codex does not ask the same Claude review enablement question again
- **AND** Codex applies the disabled session overlay while reading any existing review reports as historical context

#### Scenario: Explicit command supersedes prompted decision
- **GIVEN** the session overlay records Claude review as disabled
- **WHEN** the user invokes `/opsx:claude-review-on`
- **THEN** Codex updates the session overlay to enable review
- **AND** later lifecycle commands use the updated decision
