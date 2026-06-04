# Design: add-artifact-review-dispositions

## Goals

- Preserve provider-neutral OpenAI-compatible review reports.
- Add a local formal disposition file so Codex can prove that reviewer findings were handled.
- Render compact localized status blocks for chat output.
- Keep raw JSON fields stable and English-like for machine consumers.

## Non-Goals

- Do not add another LLM reviewer.
- Do not require live provider calls for validation.
- Do not store secrets or provider credentials in reports/dispositions.
- Do not remove legacy Claude files.

## Data Model

Persist optional disposition files under:

```text
openspec/changes/<change>/reviews/<stage>-artifact-review-disposition.json
```

Shape:

```json
{
  "change": "example-change",
  "stage": "design",
  "reviewReport": "design-artifact-review.json",
  "language": "ru",
  "items": [
    {
      "source": "mustFix",
      "index": 0,
      "finding": "...",
      "status": "fixed",
      "reason": "Исправлено в design.md",
      "target": "openspec/changes/example-change/design.md"
    }
  ]
}
```

Allowed statuses: `fixed`, `deferred`, `non_actionable`, `user_blocked`.

`deferred` requires a non-empty `target`. `user_blocked` is the only status that leaves progress blocked until the user answers.

## Status Rendering

Prompts/skills instruct Codex to render a compact localized block. Russian example:

```text
---
Ревью артефакта — test-plan: пройдено.
Стоимость: 0,82 ₽, всего по изменению: 1,31 ₽ / 3 вызова, бюджет: без лимита.
Замечания: критичных 0, желательных 0, предупреждений 0, вопросов пользователю 0 — всё закрыто.
---
```

The helper provides raw and display values. Codex selects the chat language and translates labels. The implementation does not need automatic language detection beyond documenting the rule; prompts already know the conversation language.

## Validation

`scripts/openspec-artifact-review --disposition-summary --change <change> [--stage <stage>]` validates persisted reports and disposition files locally. It returns non-zero when unresolved findings exist unless all findings are dispositioned and no `user_blocked` status remains.

`scripts/check-overlay` adds a fixture for:

- RUB upward rounding to kopecks;
- compact Russian example in docs/prompts;
- unresolved finding detection;
- closed disposition summary.

## Compatibility

- Existing review reports remain valid.
- Existing changes without disposition files are unaffected unless they have active review findings and a caller explicitly validates disposition state.
- Cost summary remains ledger-first.
