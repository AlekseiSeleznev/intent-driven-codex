## Context

The validation report shows that OpenSpec specs, schema validation, and overlay smoke checks are green, but public documentation and release/config metadata lag behind the implemented overlay. The change is documentation/config focused and must preserve existing architecture boundaries:

- OpenSpec CLI remains unpatched and owns lifecycle state.
- The Codex overlay owns prompts, skills, helper scripts, installation docs, and workflow guidance.
- `CONSTITUTION.md`, `CONTEXT.md`, `ARCHITECTURE.md`, and `adr/` remain persistent project context outside OpenSpec change artifacts.
- No secrets are required; `.secrets.local.env` must not be read.

## Goals / Non-Goals

**Goals:**

- Close validation report findings F-01 through F-14.
- Make README/README.ru state, archive list, ADR summaries, command docs, and repository structure reflect current repository state.
- Make Greenfield/manual install documentation executable and consistent with `scripts/install-overlay` and `scripts/check-overlay`.
- Align tracked `openspec/config.yaml` with the installer-generated intent-driven config.
- Bump public version metadata to `0.1.4` and introduce `CHANGELOG.md` for recent 2026-05-30 capabilities.
- Add lightweight drift-prevention checks where practical.

**Non-Goals:**

- Do not change OpenSpec CLI internals.
- Do not change product behavior outside docs/config/checks needed for the findings.
- Do not create a new durable ADR unless implementation reveals a new hard-to-reverse architecture decision.
- Do not push remote/GitHub.

## Decisions

1. **Implement as one consolidated change.**
   - The findings share one source report and mostly touch public docs/config state. Consolidating keeps critical install fixes from waiting behind multiple lifecycle cycles.

2. **Use `0.1.4` as the next version.**
   - Recent archived changes added new public capabilities: optional Claude artifact review, session controls, automatic local checkpoint helpers, and Claude review cost summaries. This is minor-version scope for a reusable template.

3. **Make installer config the canonical baseline for tracked config.**
   - Update `openspec/config.yaml` so this repository uses the same `context` and hardened artifact rules installed into target projects.
   - Do not remove project-specific rules unless they conflict; keep them equivalent or richer.

4. **Prefer generated/factual state over hand-maintained claims.**
   - README active changes should match `openspec list --json`.
   - README archive list should either include all archived changes or point to the archive directory rather than stale counts.
   - In-force ADR summaries should match `adr/README.md` and `ARCHITECTURE.md`.

5. **Fix install docs by making the installer the primary path.**
   - Greenfield docs must use `scripts/install-overlay <target> --force-overlay` after `openspec init` because OpenSpec creates placeholder `.codex/` files.
   - Manual install docs should list all helper/config files required by `scripts/check-overlay` or explicitly defer to the installer as source of truth.
   - Post-install context setup should be review/edit guidance, not blind `cp` commands.

6. **Drift prevention stays lightweight.**
   - Extend `scripts/check-overlay` with deterministic checks for known easy regressions: bad `script/check-overlay` spelling, installer/manual docs mentions of required helper files, README command discoverability, and config alignment markers.
   - Avoid heavy EN/RU semantic diffing or brittle full README parsing in this change.

## Risks / Trade-offs

- README/README.ru edits are broad and can drift again; lightweight checks reduce but do not eliminate that risk.
- Manual install docs can become verbose if every copied file is listed; however, explicitness is justified because missing helper files break `check-overlay`.
- `0.1.4` bump is a release decision; grill resolved that it matches the public feature additions already present.
- Full install-path smoke tests can be expensive; apply should include the strongest local checks practical without external services.

## Migration Plan

1. Update docs and config in focused groups following the validation report grouping.
2. Add or update lightweight drift checks in `scripts/check-overlay`.
3. Add `CHANGELOG.md` and bump `VERSION`/README version mentions to `0.1.4`.
4. Run targeted grep/config checks, OpenSpec validation, schema validation, full overlay checks, diff whitespace checks, tracked-secret checks, and git status.
5. Rollback is a normal local git revert; no data migration is required.

## Open Questions

None.
