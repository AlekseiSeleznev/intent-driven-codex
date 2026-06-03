# Отчёт о валидации проекта `intent-driven-codex`

> **Дата:** 2026-05-30 · **Проверенный коммит:** `50af005` (ветка `main`) ·
> **Версия проекта:** `0.1.3` (`VERSION`)
> **Инструменты:** OpenSpec `1.3.1`, Claude Code `2.1.157`, Python `3.12.3`

Документ — результат полной валидации шаблона **Intent-Driven Codex** (overlay
для OpenSpec + Codex). Он разделён на две части:

1. **Что прошло проверку** — функциональный слой (схема, спецификации, smoke-тесты).
2. **Найденные проблемы** — ошибки, противоречия и неточности с точными ссылками
   `файл:строка` и рекомендациями.
3. **Рекомендации в формате OpenSpec-изменений** — готовые «зёрна» для `/opsx:new`,
   чтобы исправления прошли через собственный lifecycle проекта.

---

## 1. Итоговая оценка

| Слой | Вердикт | Комментарий |
| --- | --- | --- |
| OpenSpec-схема `intent-driven` | ✅ Проходит | `openspec schema validate intent-driven` — valid; артефакты `proposal → specs → grill → design → design-review → adr → test-plan → tasks`. |
| Канонические спецификации | ✅ Проходит | `openspec validate --all --strict` → **12 passed, 0 failed**. |
| Smoke-проверка overlay | ✅ Проходит | `scripts/check-overlay` — все слои green, временный change создаётся и удаляется. |
| Скрипты (`bash -n`, dry-run, фикстуры) | ✅ Проходит | session-state, git-checkpoint, claude-review проверены без обращения к Claude. |
| Секреты / `.gitignore` | ✅ Корректно | локальные секреты не трекаются, `.secrets.local.env` и `.codex/session/` игнорируются. |
| **Документация (README, INSTALL, ARCHITECTURE, ADR)** | ⚠️ **Требует правок** | внутренние противоречия, устаревшие таблицы, **2 ошибки в инструкции по установке**, рассинхрон версии. |

**Главный вывод:** код и спецификации в порядке — рассинхронизирована
**документация и инструкции по установке**. Две проблемы (F-05, F-06)
приводят пользователя к **нерабочей установке**, поэтому помечены критическими.

---

## 2. Реестр находок

| ID | Severity | Область | Файл(ы) | Суть |
| --- | --- | --- | --- | --- |
| F-01 | 🟠 Противоречие | README | `README.md:82` ↔ `:86`; `README.ru.md:83` ↔ `:87` | «нет активных изменений» против таблицы «активное изменение = `add-claude-artifact-review`». |
| F-02 | 🟡 Неточность | README | `README.md:90`; `README.ru.md:91` | список архива — 6 изменений, фактически **9**. |
| F-03 | 🟠 Противоречие | README | `README.md:93` ↔ `:522-524`; `README.ru.md:94` ↔ `:524-526` | один и тот же набор in-force ADR описан двумя способами (0001/0003/0005 против 0001/0003/0005/0006/0007). |
| F-04 | 🟡 Неточность | README | `README.md:597,608-610`; `README.ru.md:601` | «Repository structure» устарел: ADR `0001–0003` вместо `0001–0007`, нет `CONTEXT.md`, нет `openspec-git-checkpoint`/`openspec-session-state`. |
| F-05 | 🔴 Критично | Установка | `README.md:558`; `README.ru.md:561` | Greenfield-команда без `--force-overlay` → placeholder-промпты OpenSpec не заменяются. |
| F-06 | 🔴 Критично | Установка | `INSTALL_CODEX.md:141-153` | «Manual copy procedure» не содержит файлов, без которых `check-overlay` падает. |
| F-07 | 🟡 Неточность | Установка | `INSTALL_CODEX.md:31-45` | блок `cp` дублирует установщик и перезаписывает только что установленные файлы. |
| F-08 | 🟡 Неточность | Конфиг | `openspec/config.yaml` ↔ `scripts/install-overlay:124-149` | сам шаблон использует более бедный `config.yaml`, чем генерирует установщик. |
| F-09 | 🟡 Неточность | Релиз | `VERSION`, `README.md:58-95` | `0.1.3` не отражает 3 зафиксированных после него фичи; нет `CHANGELOG`. |
| F-10 | ⚪ Мелочь | Docs | `docs/update-safety.md:14` | опечатка `script/check-overlay` → `scripts/check-overlay`. |
| F-11 | ⚪ Мелочь | Docs/ADR | `ARCHITECTURE.md:25`; `adr/README.md:13` | сломанное предложение про supersession ADR 0007 (лишняя точка, вклеенный заголовок). |
| F-12 | ⚪ Мелочь | Docs | `docs/lifecycle.md:13` | в колонке «Depends on» у `design` указан только `grill` (по схеме — `proposal, specs, grill`). |
| F-13 | ⚪ Мелочь | README | `README.md:304-317` | таблица команд не включает `/opsx:claude-review-*` (описаны только в конце файла). |
| F-14 | ⚪ Мелочь | README | `README.md:496` | скилл назван «Architecture diagrams», в остальных местах — `c4-diagrams`. |

Severity: 🔴 критично (ведёт к нерабочей установке) · 🟠 противоречие (внутренняя
несогласованность) · 🟡 неточность (устарело/неверно, но не ломает) · ⚪ мелочь.

---

## 3. Детализация находок

### 🔴 F-05 — Greenfield-инструкция без `--force-overlay` ведёт к неполной установке

**Где:** `README.md:558`, `README.ru.md:561`.

```bash
# README (Greenfield):
/path/to/intent-driven-codex/scripts/install-overlay /path/to/new-project
```

**Проблема.** Установщик — no-overwrite: при коллизии с непустым файлом он печатает
`SKIP existing` и **не заменяет** его (`scripts/install-overlay:43-58`). На Greenfield
сразу после `openspec init . --tools codex` каталог `.codex/` уже содержит
placeholder-файлы от OpenSpec, поэтому без `--force-overlay` промпты/скиллы overlay
**не устанавливаются**.

**Доказательство противоречия — внутри самого проекта:**
- `INSTALL_CODEX.md:12` для Greenfield использует `--force-overlay`;
- `README.md:73` сам пишет: *«Greenfield install support … with `scripts/install-overlay --force-overlay`»*;
- usage самого скрипта: *«use --force-overlay for a Greenfield OpenSpec/Codex init where generated placeholder prompts/skills should be replaced»* (`scripts/install-overlay:10-12`).

**Фикс.** Привести Greenfield-блок в обоих README к виду `INSTALL_CODEX.md`:
```bash
scripts/install-overlay /path/to/new-project --force-overlay
```

---

### 🔴 F-06 — «Manual copy procedure» не содержит файлов, обязательных для `check-overlay`

**Где:** `INSTALL_CODEX.md:141-153`.

Список ручного копирования включает из скриптов только `scripts/check-overlay`
и **не включает** файлы, которые реально копирует установщик
(`scripts/install-overlay:99-106`):

| Нужен для прохождения `check-overlay` | Есть в установщике | Есть в manual-списке |
| --- | :---: | :---: |
| `scripts/openspec-claude-review` | ✅ | ❌ |
| `.codex/openspec-claude-review.json` | ✅ | ❌ |
| `scripts/openspec-session-state` | ✅ | ❌ |
| `scripts/openspec-git-checkpoint` | ✅ | ❌ |
| `AGENTS.md` | ✅ | ❌ |
| `docs/lifecycle.md`, `docs/update-safety.md` | ✅ | ❌ |
| строка `.codex/session/` в `.gitignore` | ✅ | ❌ |

**Последствие.** Установка строго по «ручной» инструкции **проваливает** `check-overlay`,
который жёстко падает (`fail`), если отсутствует любой из:
`scripts/openspec-claude-review` (`check-overlay:125-129`),
`.codex/openspec-claude-review.json` (`:130`),
`scripts/openspec-session-state` (`:144-148`),
`scripts/openspec-git-checkpoint` (`:174-178`).

**Фикс.** Синхронизировать manual-список с фактическим набором установщика
(добавить 4 файла + правило `.gitignore`) **или** написать «ручной» путь как
«скопируйте всё, что копирует `install-overlay`» и оставить установщик единственным
источником истины.

---

### 🟠 F-01 — Противоречие про активные изменения

**Где:** `README.md:82` ↔ `README.md:86`; то же в `README.ru.md:83` ↔ `:87`.

- Текст: *«There are currently no active OpenSpec changes.»* (`:82`)
- Таблица: `| Active OpenSpec changes | add-claude-artifact-review |` (`:86`)

**Факт:** `openspec list --json` → `{"changes":[]}`; в `openspec/changes/` только
`archive/`. Изменение `add-claude-artifact-review` **заархивировано**
(`openspec/changes/archive/2026-05-30-add-claude-artifact-review/`).

**Фикс.** В ячейке таблицы заменить значение на `None` / `—`, согласовать со строкой 82.

---

### 🟠 F-03 — Набор in-force ADR в README описан двумя несовместимыми способами

**Где (EN):** `README.md:93` (таблица) против `README.md:522-524` (блок «ADR model»).
**Где (RU):** `README.ru.md:94` против `:524-526`.

- Таблица (`:93`) — **верно**: in-force = `0001, 0003, 0005, 0006, 0007` (0002, 0004 superseded).
- Блок «ADR model» (`:522-524`) — **устарел**: перечислены только `0001, 0003, 0005`.

**Источник истины** (совпадают между собой): `adr/README.md:7-18` и
`ARCHITECTURE.md:7-25` → in-force = `0001, 0003, 0005, 0006, 0007`.

**Фикс.** Обновить блок «ADR model» в обоих README, добавив `0006` и `0007`.

---

### 🟡 F-02 — Неполный список архива

**Где:** `README.md:90`, `README.ru.md:91` — перечислено 6 изменений.
**Факт:** в `openspec/changes/archive/` — **9**. Отсутствуют:
- `2026-05-30-add-claude-artifact-review`
- `2026-05-30-add-claude-review-cost-summary`
- `2026-05-30-add-default-git-automation-and-claude-session-controls`

**Фикс.** Дополнить ячейку либо заменить ручной список на отсылку к
`openspec/changes/archive/` (чтобы не повторять рассинхрон в будущем).

---

### 🟡 F-04 — Устаревший раздел «Repository structure»

**Где:** `README.md:595-617`, `README.ru.md:~599-621`.

- `README.md:597`: `0001-*.md, 0002-*.md, 0003-*.md` — фактически `0001–0007`.
- В дереве `scripts/` (`:608-610`) нет `openspec-git-checkpoint` и `openspec-session-state`.
- В дереве нет `CONTEXT.md` (хотя файл существует и является частью project context),
  `AGENTS.md`, `INSTALL_CODEX.md`.

**Фикс.** Перегенерировать дерево; для ADR использовать `0001-*.md … 0007-*.md`
или формулировку «`NNNN-*.md` durable ADRs».

---

### 🟡 F-07 — Greenfield: блок `cp` дублирует установщик и нарушает no-overwrite

**Где:** `INSTALL_CODEX.md:31-45`.

После запуска установщика инструкция предлагает:
```bash
cp /path/to/intent-driven-codex/CONSTITUTION.md ./CONSTITUTION.md
cp /path/to/intent-driven-codex/ARCHITECTURE.md ./ARCHITECTURE.md
...
```
Но установщик **уже** копирует `CONSTITUTION.md`, `ARCHITECTURE.md`, `.secrets.example.env`
когда их нет (`scripts/install-overlay:94-97`). `cp` без `-n` **перезапишет** их и
противоречит правилу строкой ниже: *«Only copy … when the target project does not
already have one»* и общему принципу no-overwrite.

**Фикс.** Убрать блок `cp` или переписать как «откройте и заполните установленные
`CONSTITUTION.md`/`ARCHITECTURE.md`» (review-and-edit, без повторного копирования).

---

### 🟡 F-08 — Сам шаблон использует более бедный `openspec/config.yaml`, чем генерирует установщик

**Где:** `openspec/config.yaml` против `scripts/install-overlay:124-149`.

- В репозитории `config.yaml` содержит правила только для
  `proposal/specs/design/adr/tasks` и **не имеет** блока `context:`.
- Установщик пишет целевым проектам конфиг с блоком `context:` и правилами для
  `grill/design/design-review/adr/test-plan/tasks`.

То есть шаблон не «ест свою еду»: новые проекты получают более полный конфиг, чем
сам эталонный репозиторий.

**Фикс.** Обновить `openspec/config.yaml` репозитория до уровня генерируемого
(добавить правила `grill`, `design-review`, `test-plan` и блок `context:`).

---

### 🟡 F-09 — `VERSION` 0.1.3 не отражает зафиксированную функциональность; нет `CHANGELOG`

**Где:** `VERSION` (= `0.1.3`, последний bump в коммите `0179a84`); нарратив
`README.md:58-95` («Current development state») описывает только hardening v0.1.3.

После v0.1.3 заархивированы **3 крупных изменения** (с новыми ADR 0006/0007,
скриптами `openspec-claude-review`/`openspec-session-state`/`openspec-git-checkpoint`,
спеками `claude-artifact-review`/`claude-review-session-controls` и промптами
`opsx-claude-review-*`):
- Claude artifact review;
- Claude review cost summary;
- default git automation + Claude session controls.

Бейджи и раздел состояния остаются на «v0.1.3». `CHANGELOG.md` отсутствует.

**Фикс.** Поднять `VERSION` (например, `0.2.0` — добавлены новые возможности по
semver), обновить бейджи и «Current development state», завести `CHANGELOG.md`.

---

### ⚪ F-10 — Опечатка `script/check-overlay`

**Где:** `docs/update-safety.md:14` — `` `script/check-overlay` `` → `` `scripts/check-overlay` ``.

### ⚪ F-11 — Сломанное предложение про supersession ADR 0007

**Где:** `ARCHITECTURE.md:25`, `adr/README.md:13`.
Текущий текст: *«… ADR 0001 remains otherwise in force; ADR 0007. Adopt Automatic
Lifecycle Checkpoints and Claude Review Session Controls only narrows …»* — лишняя
точка после «ADR 0007» и вклеенный заголовок ADR ломают предложение (похоже на
артефакт шаблонной вставки). **Фикс:** переписать одним связным предложением.

### ⚪ F-12 — `design` «Depends on» в lifecycle.md

**Где:** `docs/lifecycle.md:13` — указано `grill`, тогда как по схеме
(`schema.yaml:114-117`) и `README.md:291` зависимости — `proposal, specs, grill`.
Допустимое сокращение, но расходится с другими документами. **Фикс:** указать полный
набор для единообразия.

### ⚪ F-13 — Команды `/opsx:claude-review-*` не в основной таблице команд

**Где:** `README.md:304-317` — 5 промптов управления Claude-review описаны только в
конце файла (`:652-654`). **Фикс:** добавить их в таблицу команд или дать перекрёстную ссылку.

### ⚪ F-14 — Несогласованное имя скилла диаграмм

**Где:** `README.md:496` называет скилл «Architecture diagrams», тогда как
`AGENTS.md:81` и `docs/lifecycle.md:84` используют id `c4-diagrams`. **Фикс:** унифицировать имя.

---

## 4. Рекомендации в формате OpenSpec-изменений

Поскольку проект сам построен на Intent-Driven OpenSpec, исправления стоит провести
через собственный lifecycle (`/opsx:new → … → /opsx:apply → /opsx:verify → /opsx:archive`).
Ниже — 5 связных «зёрен» для `/opsx:new`.

### Изменение 1 — `fix-readme-state-drift`
**Закрывает:** F-01, F-02, F-03, F-04, F-13, F-14.
**Why:** README (EN+RU) рассинхронизирован с фактическим состоянием репозитория.
**What:** обновить ячейки «Active/Archived changes», блок «ADR model», «Repository
structure»; добавить `/opsx:claude-review-*` в таблицу команд; унифицировать `c4-diagrams`.
**Проверка:** значения в README совпадают с `openspec list --json`,
`ls openspec/changes/archive`, `adr/README.md`, `ls adr/ scripts/`.

### Изменение 2 — `fix-install-instructions`
**Закрывает:** F-05, F-06, F-07.
**Why:** инструкции приводят к нерабочей установке.
**What:** добавить `--force-overlay` в Greenfield (оба README); синхронизировать
«Manual copy procedure» с реальным набором `install-overlay`; убрать/переписать `cp`-блок.
**Проверка:** прогнать Greenfield и manual-путь в чистом каталоге → `scripts/check-overlay`
зелёный.

### Изменение 3 — `align-template-openspec-config`
**Закрывает:** F-08.
**What:** привести `openspec/config.yaml` репозитория к конфигу, который генерирует
установщик (правила `grill`/`design-review`/`test-plan` + блок `context:`).
**Проверка:** `openspec schema validate intent-driven`; diff против heredoc из `install-overlay`.

### Изменение 4 — `bump-version-and-add-changelog`
**Закрывает:** F-09.
**What:** поднять `VERSION` до `0.2.0`, обновить бейджи и «Current development state»,
завести `CHANGELOG.md` с записями для трёх изменений 2026-05-30.
**Проверка:** версия в `VERSION`, бейджах README и нарративе совпадают.

### Изменение 5 — `polish-context-docs`
**Закрывает:** F-10, F-11, F-12.
**What:** опечатка `scripts/`, переписать предложение про ADR 0007, полные зависимости
`design` в lifecycle.md.
**Проверка:** `grep -rn "script/check-overlay"` пусто; предложения читаются корректно.

---

## 5. Профилактика повторного рассинхрона (рекомендация по процессу)

Большинство находок — это **drift документации от состояния репозитория**. Чтобы он не
накапливался, рекомендую расширить `scripts/check-overlay` (или добавить
`scripts/check-docs`) автоматическими сверками — и включить их в `Verification Rules`
конституции:

1. Список архива в README == `ls openspec/changes/archive/`.
2. Значение «Active changes» в README == `openspec list --json`.
3. Набор in-force ADR в README == `adr/README.md`/`ARCHITECTURE.md`.
4. «Manual copy procedure» в `INSTALL_CODEX.md` == набор путей, копируемых `install-overlay`
   (единый источник — список путей в скрипте).
5. EN/RU README не расходятся по числовым/списочным фактам (таблицы состояния).

Это превращает «человеческую дисциплину обновления README» в проверяемый gate —
в духе самого проекта.

---

## Приложение A — выполненные команды валидации

```text
openspec --version                      # 1.3.1
openspec schemas --json                 # intent-driven (source: project) + spec-driven (package)
openspec validate --all --strict        # 12 passed, 0 failed
openspec schema validate intent-driven  # valid
scripts/check-overlay                    # все слои ✓, smoke change создан и удалён
git status --short                       # чисто до и после проверок
```

## Приложение B — сводка по серьёзности

- 🔴 Критично: **2** (F-05, F-06) — нерабочая установка.
- 🟠 Противоречия: **2** (F-01, F-03).
- 🟡 Неточности: **5** (F-02, F-04, F-07, F-08, F-09).
- ⚪ Мелочи: **5** (F-10 … F-14).

**Функциональное ядро проекта валидно.** Приоритет правок: F-05 и F-06 (установка),
затем README-drift (F-01–F-04, F-09), затем остальное.
