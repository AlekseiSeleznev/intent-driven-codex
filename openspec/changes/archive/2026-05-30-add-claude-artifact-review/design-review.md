## Context Read

- `openspec/changes/add-claude-artifact-review/proposal.md`
- `openspec/changes/add-claude-artifact-review/specs/claude-artifact-review/spec.md`
- `openspec/changes/add-claude-artifact-review/specs/codex-opsx-workflow/spec.md`
- `openspec/changes/add-claude-artifact-review/specs/intent-driven-schema/spec.md`
- `openspec/changes/add-claude-artifact-review/specs/git-discipline/spec.md`
- `openspec/changes/add-claude-artifact-review/grill.md`
- `openspec/changes/add-claude-artifact-review/design.md`
- `CONSTITUTION.md`
- `CONTEXT.md`
- `ARCHITECTURE.md`
- `adr/README.md`
- In-force durable ADRs: `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`, `adr/0003-formalize-project-context-entrypoints.md`, `adr/0005-adopt-matt-grill-and-tdd-gates.md`
- Existing specs: `openspec/specs/codex-opsx-workflow/spec.md`, `openspec/specs/intent-driven-schema/spec.md`, `openspec/specs/git-discipline/spec.md`, `openspec/specs/context-aware-review/spec.md`, `openspec/specs/test-first-verification/spec.md`, `openspec/specs/overlay-update-safety/spec.md`
- Existing scripts and prompts list under `.codex/prompts`, `.codex/skills`, and `scripts/`
- `scripts/check-overlay` and `scripts/install-overlay` implementation style
- Local Claude CLI help/official docs summary captured during proposal/spec/grill: `claude -p`, `--model`, `--effort`, structured JSON output, budgets, and permission/tool controls are available.

## Design Summary

- The design keeps Claude review outside OpenSpec CLI by adding a Codex-overlay helper script, `scripts/openspec-claude-review`.
- Review configuration is a dependency-free JSON file at `.codex/openspec-claude-review.json`, with global enablement and per-stage overrides for model, effort, fallback, budget, and policy.
- The default review input mode bundles selected artifact/context contents into stdin and disables Claude tools; read-only tool access is a later optional mode for large contexts.
- Reports are structured JSON files under `openspec/changes/<change>/reviews/<stage>-claude-review.json` and remain auxiliary context rather than schema lifecycle artifacts.
- The recommended balanced profile is Sonnet-first, with Opus 4.8 high effort reserved for architecture-sensitive `design-review`, `adr`, and `verify` stages.

## Question Loop

No material user question was required during post-design review.

Stress-test decisions and resolutions:

1. **Does the design violate the OpenSpec/Codex boundary?**
   - **Recommended answer:** No.
   - **Rationale:** The helper is called by Codex skills/prompts and OpenSpec remains responsible only for status, instructions, validation, and archive mechanics.
   - **Resolution:** Design is consistent with ADR 0001 and `ARCHITECTURE.md`.

2. **Does `enabled: false` conflict with the user asking to connect Claude?**
   - **Recommended answer:** No; ship safe disabled defaults with documented stage profiles, then let projects opt in.
   - **Rationale:** Template users may lack Claude auth; a default-on external reviewer would break Greenfield/Brownfield installs. The config still contains the recommended model/effort map and can be enabled with one non-secret value.
   - **Resolution:** Keep top-level `enabled: false` in the default tracked config.

3. **Should review reports become OpenSpec artifacts?**
   - **Recommended answer:** No.
   - **Rationale:** Adding schema lifecycle artifacts for each review stage would complicate the OpenSpec graph and risk replacing existing required gates. Reports are evidence/context that can be checkpointed and verified without becoming lifecycle nodes.
   - **Resolution:** Keep reports under `openspec/changes/<change>/reviews/` as auxiliary tracked context.

4. **Should the helper allow Claude tools by default?**
   - **Recommended answer:** No.
   - **Rationale:** Artifact review needs deterministic, bounded input and should not mutate files. Bundled stdin with `--tools ""` is safer. Read-only tools can be added as an explicit advanced input mode.
   - **Resolution:** Keep bundled input and disabled tools as the default.

## Design Findings

- **Architecture fit:** Good. The helper/prompt integration matches the existing overlay boundary and does not require OpenSpec CLI changes.
- **Secret handling:** Good with one implementation requirement: the helper needs an explicit denylist for `.secrets.local.env`, `.env.local`, local Claude transcripts/state, and any future local secret patterns from the constitution.
- **Configuration safety:** Good. JSON avoids parser dependencies and top-level disabled default avoids requiring Claude auth for template installs.
- **Model/effort handling:** Good. The design accounts for multiple Opus/Sonnet variants, aliases, custom models, and unsupported effort handling.
- **Verification feasibility:** Good. `--validate-config` and `--dry-run` allow `scripts/check-overlay` to validate integration without external Claude calls.
- **Report lifecycle:** Acceptable. Reports under the change directory will archive with the change. Because they are auxiliary evidence, apply/verify prompts must explicitly read them when present.
- **User experience risk:** Moderate. Per-stage config is powerful but can be noisy. Documentation should provide balanced/cheap/strict presets or examples so users do not have to design from scratch.

## Document Updates Applied

No OpenSpec artifact edits were required during design-review.

The existing proposal/specs/grill/design already cover the material findings:

- Claude review is auxiliary and configurable.
- Full model IDs are preferred for reproducibility, aliases remain supported.
- Effort validation is model-aware.
- Review inputs are bounded and secret-safe.
- Reports are structured and policy-gated.
- The integration belongs in the Codex overlay/helper layer.

## Document Updates Required Before Next Gate

None.

## ADR Candidates

- **Adopt Claude Code print-mode artifact review as an optional Codex-overlay reviewer gate.**
  - This is durable because it adds an external model integration pattern across lifecycle workflows and preserves the OpenSpec/Codex boundary.
- **Adopt pinned-model-first, stage-specific Claude reviewer configuration.**
  - This is durable because it decides how the project balances reproducibility, cost, and quality for secondary model review over time.

These candidates should be evaluated in `adr.md`. A top-level durable ADR is likely warranted because the integration affects architecture boundaries and workflow policy, not only implementation details.

## Open Questions

None.
