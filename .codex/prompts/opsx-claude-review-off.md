---
description: Legacy disabled Claude review command (off)
argument-hint: none
---

Claude-specific artifact review is legacy-disabled in this overlay. Do not enable or invoke `scripts/openspec-claude-review` from current intent-driven workflows.

Use the provider-neutral command instead: `/opsx:review-off`. It manages `.codex/openspec-artifact-review.json` overlays through `scripts/openspec-session-state --review ...` and `scripts/openspec-artifact-review`.
