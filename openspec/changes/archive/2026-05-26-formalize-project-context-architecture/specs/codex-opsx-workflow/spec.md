## ADDED Requirements

### Requirement: Opsx workflows load current architecture context
Architecture-sensitive `/opsx:*` workflows SHALL read the root current architecture snapshot and in-force durable ADR context before creating or relying on architecture decisions.

#### Scenario: New change has architecture impact
- **GIVEN** a user starts or continues an OpenSpec change that affects architecture, integrations, runtime boundaries, external systems, or project-wide rules
- **WHEN** Codex prepares proposal, specs, design, ADR review, tasks, apply, verify, or archive work
- **THEN** Codex reads `ARCHITECTURE.md` when present
- **AND** Codex reads `adr/README.md` and relevant in-force durable ADR files when the task needs decision context

#### Scenario: Architecture snapshot is missing
- **GIVEN** `ARCHITECTURE.md` is missing
- **WHEN** Codex starts architecture-sensitive work
- **THEN** Codex reports that the current architecture snapshot is missing
- **AND** Codex may continue only for bootstrap-safe architecture setup or with an explicit one-time override

## MODIFIED Requirements

### Requirement: Opsx workflows run constitution preflight
All `/opsx:*` workflows SHALL run a shared Codex-layer constitution preflight before taking workflow actions, while keeping OpenSpec CLI responsibility unchanged.

#### Scenario: Preflight reads constitution before workflow action
- **GIVEN** `CONSTITUTION.md` exists at the project root
- **WHEN** the user invokes an `/opsx:*` workflow
- **THEN** Codex reads the constitution before acting on OpenSpec state
- **AND** Codex applies relevant project rules to the requested workflow

#### Scenario: Missing constitution allows only bootstrap-safe actions
- **GIVEN** `CONSTITUTION.md` is missing
- **WHEN** the user invokes an `/opsx:*` workflow
- **THEN** Codex reports that the constitution is missing
- **AND** Codex offers to create it from a template or migrate legacy `PROJECT_CONSTITUTION.md` when present
- **AND** Codex allows only bootstrap-safe or diagnostic actions such as exploration, starting a constitution-related change, creating the constitution from a template, or checking overlay health
- **AND** Codex blocks dependent planning, apply, verify, archive, sync, and bulk workflows unless the user gives an explicit one-time override

#### Scenario: Preflight stops on constitution conflict
- **GIVEN** `CONSTITUTION.md` contains a rule relevant to the requested workflow
- **AND** the requested task or OpenSpec artifact conflicts with that rule
- **WHEN** Codex runs the constitution preflight
- **THEN** Codex stops before modifying project files or external systems
- **AND** Codex asks the user how to resolve the conflict

### Requirement: Opsx workflows use external credentials only when needed
The constitution preflight MUST load local `.secrets.local.env` values only when the requested workflow needs access to an external system listed in `CONSTITUTION.md`, and MUST never print secret values.

#### Scenario: Workflow does not need external access
- **GIVEN** `CONSTITUTION.md` lists an external system with credential variables
- **AND** the requested workflow does not need that system
- **WHEN** Codex runs the constitution preflight
- **THEN** Codex does not need to read `.secrets.local.env` for that system
- **AND** Codex may continue using the non-secret constitution context

#### Scenario: Workflow needs external access with complete credentials
- **GIVEN** `CONSTITUTION.md` lists an external system required by the current workflow
- **AND** `.secrets.local.env` defines all required credential variables for that system
- **WHEN** Codex prepares to use the external system
- **THEN** Codex may read the needed local secret values
- **AND** Codex does not print those values in responses, artifacts, logs, diffs, or commits

#### Scenario: Workflow needs external access with missing credentials
- **GIVEN** `CONSTITUTION.md` lists an external system required by the current workflow
- **AND** `.secrets.local.env` is missing or lacks required variables
- **WHEN** Codex prepares to use the external system
- **THEN** Codex stops before making the external-system call
- **AND** Codex reports the missing variable names without revealing any secret values
