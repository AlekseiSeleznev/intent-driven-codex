## Why

Generated OpenSpec artifacts are currently reviewed by Codex skills, OpenSpec validation, and mandatory grill/TDD gates, but there is no configurable independent Claude Code reviewer that can cross-check selected artifacts before dependent lifecycle stages consume them. Adding optional `claude -p` review support would let projects balance cost and quality by choosing which lifecycle stages receive a second-model review and which Claude model/effort profile each stage uses.

## What Changes

- Add a configurable Claude Code artifact review mechanism that can run `claude -p` after selected OpenSpec artifacts are generated and before the next dependent artifact consumes them.
- Support global defaults plus per-stage overrides for model, effort, fallback model, budget, enablement, prompt profile, and blocking policy.
- Prefer reproducible full Claude model IDs for pinned behavior, while allowing floating aliases such as `sonnet`, `opus`, and `haiku` when explicitly configured.
- Make model/effort validation stage-aware so unsupported effort levels are reported or safely omitted according to configuration.
- Return and persist structured review results that Codex can interpret as pass/warn/fail/blocked without parsing free-form prose.
- Preserve existing OpenSpec, grill-with-docs, ADR, TDD, verification, secret-handling, and git checkpoint rules; Claude review is an additional reviewer gate, not a replacement.
- Document recommended cost/quality profiles for lifecycle stages, including Sonnet-first defaults and Opus escalation for architecture-sensitive review points.

## Capabilities

- New capability: `claude-artifact-review` — configurable Claude Code review of generated OpenSpec artifacts.
- Modified capability: `codex-opsx-workflow` — `/opsx:continue`, `/opsx:propose`, `/opsx:ff`, and `/opsx:verify` should account for enabled Claude review gates when applicable.
- Modified capability: `intent-driven-schema` — schema/prompt guidance may need to mention review context and optional artifact review reports without changing OpenSpec CLI ownership.
- Modified capability: `git-discipline` — review reports and artifact changes should be checkpointed before dependent stages consume them when review is enabled and produces tracked output.

## Impact

- Affected overlay code and guidance: `.codex/skills/openspec-continue-change/SKILL.md`, `.codex/skills/openspec-propose/SKILL.md`, `.codex/skills/openspec-ff-change/SKILL.md`, `.codex/skills/openspec-verify-change/SKILL.md`, related `/opsx:*` prompts, and `scripts/check-overlay`.
- Affected schema/docs: `openspec/schemas/intent-driven/schema.yaml`, schema templates or README files if review reports become part of instructions, `docs/lifecycle.md`, `docs/update-safety.md`, `README.md`, and `README.ru.md` when user-facing behavior changes.
- Potential new helper/config files: a project-local review wrapper under `scripts/` and a non-secret tracked review configuration file under `.codex/` or `openspec/`.
- External systems and secrets: Claude Code may need local Claude authentication or API credentials already configured outside this repository. This change must not read or reveal `.secrets.local.env` unless a listed external system actually requires it, and tracked files may include only variable names or empty placeholders.
- Architecture constraints: keep OpenSpec CLI unchanged; implement review orchestration in the Codex overlay/helper layer according to ADR 0001 and the current `ARCHITECTURE.md` boundary model.
- Verification constraints: continue to run OpenSpec validation and `scripts/check-overlay` after overlay/template updates; review automation must fail safely when `claude` is unavailable, unauthenticated, over budget, or returns invalid structured output.
