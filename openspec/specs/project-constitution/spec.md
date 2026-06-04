# project-constitution Specification

## Purpose
Defines the persistent project constitution rules that Codex must read before `/opsx:*` and direct OpenSpec lifecycle actions, while keeping secret values out of Git and OpenSpec artifacts.

## Requirements
### Requirement: Project constitution is persistent project context
The template SHALL support a root-level `CONSTITUTION.md` file as Git-tracked, non-secret project context that Codex must treat as persistent rules outside the OpenSpec change artifact lifecycle. `CONSTITUTION.md` is the canonical filename; legacy `PROJECT_CONSTITUTION.md` may be treated only as migration input when the canonical file is absent.

#### Scenario: Constitution survives OpenSpec archive
- **GIVEN** a repository contains `CONSTITUTION.md` at the project root
- **WHEN** Codex archives an OpenSpec change
- **THEN** `CONSTITUTION.md` remains at the project root
- **AND** it is not moved into `openspec/changes/archive/`
- **AND** it is not treated as a change artifact

#### Scenario: Constitution is reviewed as project rules
- **GIVEN** a user updates `CONSTITUTION.md`
- **WHEN** Codex prepares a git checkpoint
- **THEN** Codex treats the file as Git-tracked project rules
- **AND** Codex does not classify it as a local secret file

#### Scenario: Legacy constitution is detected for migration
- **GIVEN** `CONSTITUTION.md` is missing
- **AND** legacy `PROJECT_CONSTITUTION.md` exists at the project root
- **WHEN** Codex runs constitution preflight
- **THEN** Codex reports that `CONSTITUTION.md` is the canonical file
- **AND** Codex offers to migrate or rename `PROJECT_CONSTITUTION.md`
- **AND** Codex does not silently maintain both files as competing rule sources

### Requirement: Constitution template covers required AI operating context
The template MUST provide constitution guidance that covers Required Technologies, MCP Servers, External Systems, Secret Handling, Documentation Sources, Verification Rules, and Additional AI Instructions.

#### Scenario: User records mandatory technologies
- **GIVEN** a project requires specific languages, frameworks, tools, or architectural technologies
- **WHEN** the user fills `CONSTITUTION.md`
- **THEN** the Required Technologies section records the mandatory choices
- **AND** Codex can use that section as project constraints during planning, apply, and verify

#### Scenario: User records MCP usage by development block
- **GIVEN** a project uses MCP servers for particular development areas
- **WHEN** the user fills `CONSTITUTION.md`
- **THEN** the MCP Servers section records server names, intended development blocks, and non-secret parameters
- **AND** Codex can determine which MCP server guidance applies to the current task

#### Scenario: User records documentation policy
- **GIVEN** a project has preferred documentation sources or lookup rules
- **WHEN** the user fills `CONSTITUTION.md`
- **THEN** the Documentation Sources section records which docs to use and how to use them
- **AND** Codex can follow those instructions when documentation is needed

#### Scenario: User records architecture context policy
- **GIVEN** a project has persistent architecture context
- **WHEN** the user fills `CONSTITUTION.md`
- **THEN** the Documentation Sources or Additional AI Instructions section can require Codex to read `ARCHITECTURE.md`, `adr/README.md`, and in-force ADRs for architecture-sensitive work

### Requirement: Constitution references local secrets without containing them
`CONSTITUTION.md` MUST NOT contain secret values and SHALL reference local `.secrets.local.env` variables for credentials needed by listed external systems.

#### Scenario: Constitution maps an external system to credential variables
- **GIVEN** the External Systems section lists a non-MCP system
- **WHEN** credentials are required for that system
- **THEN** the constitution records the required variable names
- **AND** the Secret Handling section states that the values live in `.secrets.local.env`
- **AND** no login, password, token, or service URL secret value is written into `CONSTITUTION.md`

#### Scenario: Tracked examples contain only placeholders
- **GIVEN** the repository includes a tracked secret example or template file
- **WHEN** Codex writes or reviews that file
- **THEN** it contains only variable names, comments, or empty placeholders
- **AND** it does not contain real secret values

### Requirement: Constitution conflicts stop Codex workflows
Codex MUST stop and ask the user before continuing when the project constitution conflicts with the requested task, an OpenSpec artifact, the current architecture snapshot, or an intended implementation action.

#### Scenario: Task conflicts with required technology
- **GIVEN** `CONSTITUTION.md` requires a specific technology
- **AND** the user requests an alternative that conflicts with that requirement
- **WHEN** Codex detects the conflict
- **THEN** Codex stops before modifying project files
- **AND** Codex asks the user whether to update the constitution, change the task, or explicitly override the rule

#### Scenario: Artifact conflicts with additional AI instructions
- **GIVEN** an OpenSpec artifact proposes behavior that contradicts Additional AI Instructions in `CONSTITUTION.md`
- **WHEN** Codex reads the artifact for a workflow action
- **THEN** Codex reports the contradiction
- **AND** Codex waits for user direction before proceeding
