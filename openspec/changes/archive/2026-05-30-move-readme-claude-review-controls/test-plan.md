## TDD Strategy

Use documentation-oriented command checks as the public interface. The behavior is README navigation/order, so RED/GREEN evidence is based on deterministic text-order checks plus standard OpenSpec and overlay validation.

## Vertical TDD Slices

| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| 1. English README placement | `README.md` places `## Claude review session controls` after `## Optional Claude artifact review` and before `## Project context`, not after License | Python order check fails because the section appears after `## License` | Same check passes after moving the section | Preserve command names and paragraph text |
| 2. Russian README placement | `README.ru.md` places `## Управление проверкой Claude в сессии` after `## Опциональная проверка артефактов через Claude` and before `## Контекст проекта`, not after License | Python order check fails because the section appears after `## Лицензия` | Same check passes after moving the section | Russian text remains natural and mirrors English structure |
| 3. Overlay/docs validation | Project docs remain consistent after move | Standard validation would fail on broken docs/specs/checks | `scripts/check-overlay`, OpenSpec validation, and hygiene checks pass | No unrelated README rewrites |

## Mocking / Boundary Policy

No mocks. Checks read tracked Markdown files directly and do not call external systems or Claude.

## Required Checks

- Targeted README order check for English and Russian files.
- `openspec validate move-readme-claude-review-controls --strict`
- `openspec validate --all --strict`
- `openspec schema validate intent-driven`
- `scripts/check-overlay`
- `git diff --check`
- Tracked-secret check.

## Evidence Log

- Slice 1 RED: targeted Python order check failed for `README.md` because `## Claude review session controls` appeared after `## License` instead of between optional Claude review and project context.
- Slice 2 RED: targeted Python order check failed for `README.ru.md` because `## Управление проверкой Claude в сессии` appeared after `## Лицензия` instead of between optional Claude review and project context.
- Slice 1 GREEN: targeted Python order check passed for `README.md`; controls now appear after `## Optional Claude artifact review` and before `## Project context`.
- Slice 2 GREEN: targeted Python order check passed for `README.ru.md`; controls now appear after `## Опциональная проверка артефактов через Claude` and before `## Контекст проекта`.
- Verification passed: `openspec validate move-readme-claude-review-controls --strict`, `openspec validate --all --strict`, `openspec schema validate intent-driven`, `scripts/check-overlay`, `git diff --check`, and tracked-secret check.

## TDD Exceptions

None.
