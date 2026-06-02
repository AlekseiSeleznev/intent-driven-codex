## Why

This project needs a Codex-native implementation of intent-driven development that combines the useful parts of `intent-driven-dev/openspec-schemas` and `intent-driven-dev/intent-driven-template` without depending on OpenCode. The workflow must let Codex and OpenSpec guide real feature work from intent to specs, design, ADR review, implementation tasks, verification, and archive while preserving OpenSpec as the lifecycle/source-of-truth engine.

## What Changes

- Add a project-local `intent-driven` OpenSpec schema that produces the lifecycle `proposal -> specs -> design -> adr -> tasks` and incorporates the audit fixes discovered before implementation.
- Add Codex-native `/opsx:*` commands and skills/adapters for new, propose/continue, apply, verify, sync, archive, overlay checks, and optional bulk workflows.
- Replace upstream `grill-me` behavior with `grill-with-docs` so proposal/design review is context-aware and reads OpenSpec artifacts, ADRs, docs, and relevant code.
- Add an honest per-change ADR gate at `openspec/changes/<change>/adr.md`, while durable architectural decisions remain append-only in top-level `adr/*.md`.
- Enforce mandatory git discipline checkpoints after each lifecycle artifact and hard gates before apply/archive/dependent work.
- Add validation and smoke-check tooling so the overlay survives OpenSpec updates without forking or patching OpenSpec itself.
- Document Greenfield and Brownfield behavior so the template can be installed in empty projects and existing projects safely.

## Capabilities

### New Capabilities

- `intent-driven-schema`: Project-local OpenSpec schema and templates for the Codex-adapted intent-driven lifecycle, including corrected artifact dependencies and OpenSpec-compatible spec/ADR behavior.
- `codex-opsx-workflow`: Codex slash-command prompts and skills that drive OpenSpec new/continue/propose/apply/verify/sync/archive flows without OpenCode assumptions.
- `context-aware-review`: Proposal and design review behavior using `grill-with-docs`, plus Gherkin, C4, and ADR guidance at the appropriate lifecycle stages.
- `adr-lifecycle`: Per-change ADR review plus append-only durable ADR history with supersession rather than rewriting old ADRs.
- `git-discipline`: Mandatory checkpoint and hard-gate policy for proposal, specs, design, ADR, tasks, apply, verify, sync, and archive stages.
- `overlay-update-safety`: Compatibility checks and ownership boundaries that keep this project as an overlay on OpenSpec rather than a fork.
- `template-installation`: Installer/setup documentation and smoke checks for Greenfield and Brownfield repositories.

### Modified Capabilities

- None. This repository has no canonical OpenSpec specs yet; this bootstrap change introduces the first capabilities.

## Impact

- Adds project-local OpenSpec assets under `openspec/schemas/intent-driven/` and canonical specs under `openspec/specs/` after archive.
- Adds Codex assets under `.codex/prompts/` and `.codex/skills/`.
- Adds durable ADR support under top-level `adr/`.
- Adds documentation and checks based on `FUNCTIONALITY.md`.
- Uses OpenSpec 1.3.1 public CLI behavior; does not modify or fork OpenSpec.
- Requires future implementation to account for the audit findings:
  - `design` must depend on `specs` if we want strict `proposal -> specs -> design -> adr -> tasks` ordering.
  - `adr` must generate `openspec/changes/<change>/adr.md` for per-change gate tracking.
  - Apply/tasks/verify flows must explicitly read top-level `adr/*.md` because per-change `adr.md` will be the OpenSpec-tracked artifact.
  - Spec templates must include normative requirement text with `MUST` or `SHALL` before Gherkin scenarios.
  - `/opsx:check-overlay` must use valid OpenSpec 1.3.1 commands, not bare `openspec status --json` without `--change`.
