## Context Read

- `openspec/changes/add-claude-artifact-review/proposal.md`
- `openspec/changes/add-claude-artifact-review/specs/claude-artifact-review/spec.md`
- `openspec/changes/add-claude-artifact-review/specs/codex-opsx-workflow/spec.md`
- `openspec/changes/add-claude-artifact-review/specs/intent-driven-schema/spec.md`
- `openspec/changes/add-claude-artifact-review/specs/git-discipline/spec.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`
- `adr/0003-formalize-project-context-entrypoints.md`
- `adr/0005-adopt-matt-grill-and-tdd-gates.md`
- `openspec/specs/codex-opsx-workflow/spec.md`
- `openspec/specs/intent-driven-schema/spec.md`
- `openspec/specs/git-discipline/spec.md`
- `docs/lifecycle.md`
- `docs/update-safety.md`
- Local Claude CLI capability check from this change discussion: `claude --version` returned `2.1.157 (Claude Code)` and local help exposes `--model`, `--effort`, `--json-schema`, `--output-format`, `--max-budget-usd`, `--fallback-model`, and `--permission-mode`.
- Official docs checked during exploration: Claude Code model configuration, CLI reference, Claude models overview, effort guidance, and pricing guidance.

## Plan Summary

- The change adds a new optional `claude-artifact-review` capability that runs Claude Code in non-interactive `claude -p` mode as a secondary reviewer for selected OpenSpec artifacts.
- The review is configurable globally and per lifecycle stage, including pinned or alias model selection, effort, fallback model, budget, prompt profile, report persistence, and blocking policy.
- The specs intentionally keep OpenSpec CLI unchanged and put orchestration in the Codex overlay/helper layer, preserving ADR 0001 and the current architecture boundary.
- The reviewer must produce structured pass/warn/fail/blocked output so Codex can enforce policy without interpreting free-form prose.
- Secret boundaries remain unchanged: tracked config can contain non-secret model/profile/policy values only; local auth details or tokens must stay outside artifacts and must not be read unless actually needed.

## Question Loop

No material user question was required before design.

Repository context and user-provided decisions resolved the material pre-design uncertainties:

1. **Should Claude review replace existing gates?**
   - **Recommended answer:** No. Treat Claude as an additional reviewer gate only.
   - **Rationale:** ADR 0005 makes `grill.md`, `design-review.md`, and `test-plan.md` mandatory; the specs preserve those gates and OpenSpec validation.
   - **Resolution:** Adopted in specs.

2. **Should model and effort be global only?**
   - **Recommended answer:** No. Use global defaults with per-stage overrides.
   - **Rationale:** The user explicitly requested stage-specific Claude model and effort control, and official Claude docs show cost/capability trade-offs vary by model and effort.
   - **Resolution:** Adopted in specs.

3. **Should model aliases be enough?**
   - **Recommended answer:** No. Support aliases, but prefer full model IDs for reproducible reviews.
   - **Rationale:** Claude Code aliases such as `opus` and `sonnet` intentionally resolve to provider-specific latest/recommended versions and can change over time; full model IDs give stable review behavior.
   - **Resolution:** Adopted in specs.

4. **Should unavailable Claude block every workflow?**
   - **Recommended answer:** Make this configurable per stage.
   - **Rationale:** Different projects may want strict gates for architecture-sensitive stages and best-effort warnings for low-risk stages or offline work.
   - **Resolution:** Adopted in specs as an unavailable/fallback policy.

## Resolved Terms

- **Claude artifact review** — an optional secondary review performed by Claude Code through `claude -p` for selected OpenSpec artifacts. It is reviewer context, not an OpenSpec lifecycle replacement.
- **Pinned model ID** — a full Claude model identifier such as `claude-opus-4-8` or `claude-sonnet-4-6` used when reproducible reviewer behavior is desired.
- **Model alias** — a Claude Code alias such as `opus`, `sonnet`, `haiku`, or `best` whose resolved model can vary by provider and over time.
- **Stage review policy** — per-stage configuration that determines whether review runs, which model/effort/budget it uses, and whether findings block the next artifact.

No `CONTEXT.md` update was applied because these are workflow/implementation terms for this change rather than stable project glossary terms used across the domain model.

## Document Updates Applied

- Proposal already scopes Claude review as an additional reviewer gate rather than a replacement for OpenSpec, grill, ADR, TDD, verification, or git discipline.
- Specs already include:
  - per-stage model/effort/budget/fallback/policy configuration;
  - alias and full model ID support;
  - model-aware effort validation;
  - structured `claude -p` output;
  - secret-safe review inputs;
  - policy-based blocking before dependent artifacts;
  - `/opsx:*`, schema guidance, and git checkpoint interactions.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

- **Adopt Claude Code print-mode artifact review as a Codex-overlay responsibility.**
  - Candidate because it reinforces the OpenSpec/Codex boundary from ADR 0001, affects multiple workflow entry points, introduces a durable external reviewer integration pattern, and has real trade-offs around cost, reliability, and reproducibility.
- **Adopt pinned-model-first reviewer configuration with optional aliases.**
  - Candidate because model selection policy affects reproducibility and cost over time; future readers may wonder why aliases are not the only supported configuration.

These should be evaluated during the per-change `adr.md` step after design and design-review confirm the implementation shape.

## Open Questions

None.
