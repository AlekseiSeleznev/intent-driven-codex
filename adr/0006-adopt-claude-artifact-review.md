---
id: adr-0006
status: accepted
date: 2026-05-30
decision-makers:
  - Project owner
consulted:
  - Codex
related-change:
  - openspec/changes/add-claude-artifact-review
related-adrs:
  - adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
  - adr/0003-formalize-project-context-entrypoints.md
  - adr/0005-adopt-matt-grill-and-tdd-gates.md
supersedes: []
superseded_by: []
---

# 0006. Adopt Optional Claude Artifact Review

## Status

Accepted.

## Context

ADR 0001 establishes `intent-driven-codex` as a project-local Codex/OpenSpec overlay: OpenSpec remains the lifecycle engine, while Codex prompts, skills, helper scripts, and project-local schema guidance own workflow behavior.

ADR 0003 establishes root project context entry points and local-secret boundaries. ADR 0005 hardens the lifecycle with mandatory Matt `grill-with-docs` gates and Matt TDD discipline.

The project now needs an optional independent reviewer for generated OpenSpec artifacts. The user specifically asked to investigate `claude -p` / Claude Code as the reviewer, including how to send artifact Markdown files, receive structured responses, process findings, control which stages are reviewed, and tune Claude model and effort per stage for cost/quality balance.

This decision must preserve the existing lifecycle gates. Claude review should improve confidence in generated artifacts, but it must not replace OpenSpec validation, `grill.md`, `design-review.md`, per-change ADR review, `test-plan.md`, TDD evidence, or Git checkpoint discipline.

## Decision

We will add Claude artifact review as an **optional Codex-overlay reviewer gate** implemented outside OpenSpec CLI.

The integration will use:

- a helper script such as `scripts/openspec-claude-review` to call Claude Code in non-interactive `claude -p` mode;
- a tracked non-secret JSON config such as `.codex/openspec-claude-review.json`;
- global defaults plus per-stage overrides for model, effort, fallback model, maximum budget, prompt profile, enablement, and blocking policy;
- full pinned Claude model IDs as the recommended reproducible configuration style, while still allowing aliases such as `sonnet`, `opus`, and `haiku` when a project explicitly wants floating provider defaults;
- model-aware effort validation so unsupported effort settings are rejected, downgraded, or omitted according to configured policy;
- bundled, bounded, secret-safe review inputs by default, passed to `claude -p` through stdin with Claude tools disabled;
- structured JSON/JSON-Schema output so Codex can process pass/warn/fail/blocked findings deterministically;
- auxiliary review reports under `openspec/changes/<change>/reviews/`, checkpointed with the artifact step when produced.

The default tracked configuration will be safe for template users without Claude credentials: top-level review is disabled until explicitly enabled. The config may still document a balanced profile that uses Sonnet for most routine artifact reviews and Opus for high-value architecture-sensitive stages such as `design-review`, `adr`, and `verify`.

## Consequences

### Positive

- OpenSpec artifacts can receive an independent second-model review without changing the OpenSpec artifact graph.
- Projects can tune cost, latency, and review depth per lifecycle stage instead of using one global model setting.
- Pinned model IDs make review behavior more reproducible for projects that need stable gates.
- Floating aliases remain available for projects that prefer automatic provider updates.
- Structured reports give `/opsx:verify` and later lifecycle steps auditable review evidence.
- Safe disabled defaults avoid breaking installs where Claude Code is unavailable or unauthenticated.
- Keeping the integration in the Codex overlay preserves the OpenSpec CLI boundary from ADR 0001.

### Negative / Trade-offs

- The overlay gains another helper script, config file, report format, and documentation surface to maintain.
- Claude review can add cost and latency, especially when Opus or high effort is enabled.
- A second-model reviewer can still produce false positives or false negatives; existing gates remain necessary.
- Stage-specific configuration is more powerful but more complex for users than a single global switch.
- Pinned model IDs may become unavailable over time and require documented update guidance.
- Review reports under `openspec/changes/` add tracked files that must be considered by checkpoint and archive workflows.

## Alternatives Considered

### Patch OpenSpec CLI to run Claude review

Rejected. This violates ADR 0001 and would make OpenSpec upgrades brittle. OpenSpec should keep owning lifecycle state and validation, not external reviewer orchestration.

### Make Claude review required by default

Rejected. Many installed projects may not have Claude Code installed or authenticated. Default-on review would make the template fragile for Greenfield/Brownfield users.

### Use only free-form Claude output

Rejected. Free-form prose would force Codex to infer pass/fail semantics. Structured JSON/JSON-Schema output is necessary for reliable workflow gating.

### Use only Claude model aliases

Rejected as the default guidance. Aliases are convenient, but their resolved model can vary by provider and time. Full model IDs are better for reproducible artifact review, while aliases remain an explicit opt-in.

### Allow Claude tools by default

Rejected. Artifact review should be deterministic and bounded. Bundled stdin input with tools disabled is safer. Read-only `Read`/`Glob`/`Grep` access can be an explicit advanced mode for large contexts.

### Store review config under OpenSpec schema files

Rejected. Reviewer configuration is Codex-overlay runtime policy, not OpenSpec schema structure. A `.codex/` config file better reflects ownership.

## Implementation References

- `openspec/changes/add-claude-artifact-review/`
- `scripts/openspec-claude-review`
- `.codex/openspec-claude-review.json`
- `.codex/skills/openspec-continue-change/SKILL.md`
- `.codex/skills/openspec-propose/SKILL.md`
- `.codex/skills/openspec-ff-change/SKILL.md`
- `.codex/skills/openspec-verify-change/SKILL.md`
- `.codex/prompts/opsx-continue.md`
- `.codex/prompts/opsx-propose.md`
- `.codex/prompts/opsx-ff.md`
- `.codex/prompts/opsx-verify.md`
- `scripts/check-overlay`
- `docs/lifecycle.md`
- `docs/update-safety.md`

## Revisit Criteria

Revisit this ADR if:

- OpenSpec introduces first-class external reviewer hooks that can satisfy the same requirements without patching;
- Claude Code changes CLI flags or structured output semantics in a way that invalidates the helper design;
- the project adopts a different default reviewer or multi-reviewer abstraction;
- review cost/latency proves too high for routine lifecycle use;
- Codex gains a native safe reviewer API that replaces shelling out to `claude -p`.
