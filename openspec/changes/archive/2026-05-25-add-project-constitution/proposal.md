## Why

Intent-Driven Codex projects need a durable project-level rule source for required technologies, MCP usage, external systems, secret handling, documentation sources, verification expectations, and other AI operating constraints that must not be missed during work. OpenSpec artifacts describe change intent, but repository-wide operating constraints belong outside individual changes so they survive across changes, are not archived away, and are read before any Codex workflow action.

## What Changes

- Add support for a root-level `PROJECT_CONSTITUTION.md` that users manually maintain for each project.
- Define `PROJECT_CONSTITUTION.md` as a persistent project document, not an OpenSpec change artifact, so it is never archived with `openspec/changes/<change>/`.
- Require Codex-layer `/opsx:*` prompts and supporting skills to run a shared constitution preflight before taking workflow actions; OpenSpec CLI itself remains unaware of this file.
- Update the `intent-driven` schema instructions so artifact and apply guidance consistently include constitution context without treating the constitution as an OpenSpec artifact.
- Require Codex to report a missing constitution and offer to create it from a template instead of silently continuing without project rules.
- When the constitution is missing, allow only bootstrap-safe or diagnostic actions such as exploration, starting a constitution-related change, creating the constitution from a template, or checking overlay health; block dependent planning, apply, verify, archive, sync, and bulk workflows until the constitution exists or the user gives an explicit one-time override.
- Require Codex to stop and ask the user when constitution rules conflict with the requested task or OpenSpec artifact content.
- Treat `PROJECT_CONSTITUTION.md` as a Git-tracked project rules file while prohibiting secret values inside it.
- Require the constitution to list external systems together with the credential variable names needed to access them.
- Require the constitution to include a dedicated Secret Handling section that names local `.secrets.local.env` as the place for secret values and describes how credential variables map to systems.
- Add secret-handling guidance that uses local `.secrets.local.env` for secret values such as logins, passwords, tokens, and service URLs for systems listed in the constitution, and keeps those values out of Git.
- Allow Git-tracked documentation/templates to contain secret variable names only, not secret values, including an optional example env file with empty placeholders or variable-name documentation.
- Require Codex to stop before using an external system when the constitution lists required credential variables but `.secrets.local.env` is missing or incomplete.
- Ensure the constitution template covers at least Required Technologies, MCP Servers, External Systems, Secret Handling, Documentation Sources, Verification Rules, and Additional AI Instructions.

## Capabilities

### New Capabilities
- `project-constitution`: Defines the persistent root `PROJECT_CONSTITUTION.md` contract, required sections, missing-file behavior, conflict handling, local secret-file boundaries, and the project context Codex must always consider.

### Modified Capabilities
- `codex-opsx-workflow`: `/opsx:*` prompts and related Codex skills must load and enforce `PROJECT_CONSTITUTION.md` before workflow actions, while keeping OpenSpec CLI responsibility unchanged.
- `intent-driven-schema`: Schema artifact and apply instructions must tell Codex to account for the project constitution while preserving the existing artifact lifecycle and OpenSpec-compatible validation behavior.
- `template-installation`: Installed template documentation and scaffolded project files must explain constitution setup, include safe local-secret guidance, and keep `.secrets.local.env` out of Git.
- `overlay-update-safety`: Overlay updates and checks must treat `PROJECT_CONSTITUTION.md` and local secret files as project-owned state that must not be archived, overwritten, or patched silently.

## Impact

- Affects Codex prompt files under `.codex/prompts/opsx-*.md`.
- Affects supporting skills under `.codex/skills/openspec-*/SKILL.md` where `/opsx:*` behavior is implemented.
- Affects the `intent-driven` schema guidance under `openspec/schemas/intent-driven/schema.yaml` and related templates/docs.
- Affects repository documentation such as `README.md`, `README.ru.md`, `INSTALL_CODEX.md`, and lifecycle/update-safety docs.
- May add or update project-local template files for `PROJECT_CONSTITUTION.md`, `.secrets.local.env`, and safe secret examples.
- May update `.gitignore` to ensure `.secrets.local.env` stays local-only.
- Does not require OpenSpec CLI changes; constitution loading and enforcement is a Codex-layer responsibility.
