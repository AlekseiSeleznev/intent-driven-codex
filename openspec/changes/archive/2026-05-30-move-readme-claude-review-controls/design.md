## Context

Both READMEs currently contain a detailed optional Claude artifact review section and a separate Claude review session controls section at the document end. The command table already lists the session-control commands, so the end-of-file section is redundant in placement, not content.

No code, architecture boundary, external system, or secret handling behavior changes are needed.

## Goals / Non-Goals

**Goals:**

- Move the English Claude review session controls section immediately after `## Optional Claude artifact review`.
- Move the Russian `## Управление проверкой Claude в сессии` section immediately after `## Опциональная проверка артефактов через Claude`.
- Preserve command coverage and keep Russian wording natural.

**Non-Goals:**

- Do not change Claude review defaults, models, budgets, helper behavior, or session-state scripts.
- Do not rewrite README content beyond the section move unless needed for clean flow.
- Do not change release version text.

## Decisions

- Preserve the existing section text and heading levels.
- Insert each controls section after the budget-exhaustion paragraph in the optional Claude review section.
- Remove the old end-of-file copies so Version and License return to the document ending.

## Risks / Trade-offs

- The move changes line numbers and README flow only; no runtime risk.
- If future README sections are reorganized, `scripts/check-overlay` and documentation review should still verify command discoverability and state claims.

## Migration Plan

1. Move the sections in `README.md` and `README.ru.md`.
2. Verify ordering with targeted text checks.
3. Run `scripts/check-overlay`, OpenSpec validation, diff hygiene, and tracked-secret checks.
4. Archive the change to sync canonical docs requirements.

## Open Questions

None.
