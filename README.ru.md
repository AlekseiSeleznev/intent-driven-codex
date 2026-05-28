# Intent-Driven Codex

<p align="center">
  <strong>Надстройка Codex для разработки по намерениям в проектах OpenSpec.</strong>
</p>

<p align="center">
  <a href="README.md">English</a> | <strong>Русский</strong>
</p>

<p align="center">
  <img alt="Версия" src="https://img.shields.io/badge/version-v0.1.3-blue">
  <a href="LICENSE"><img alt="Лицензия" src="https://img.shields.io/badge/license-MIT-green"></a>
  <img alt="OpenSpec" src="https://img.shields.io/badge/OpenSpec-1.3.x-2f6fed">
  <img alt="Codex" src="https://img.shields.io/badge/Codex-native-111827">
  <img alt="Intent Driven" src="https://img.shields.io/badge/Intent--Driven-ready-7c3aed">
</p>
<p align="center">
  <img src="docs/assets/intent-driven-codex-hero.png" alt="Баннер Intent-Driven Codex">
</p>

Intent-Driven Codex — это переиспользуемый шаблон, который переносит разработку
по намерениям в Codex и при этом оставляет OpenSpec движком жизненного цикла и
основным источником истины.

Проект объединяет идеи из
[`intent-driven-dev/openspec-schemas`](https://github.com/intent-driven-dev/openspec-schemas)
и
[`intent-driven-dev/intent-driven-template`](https://github.com/intent-driven-dev/intent-driven-template),
а затем адаптирует их под Codex: добавляет команды `.codex/prompts`, навыки
`.codex/skills`, локальную схему OpenSpec, правила архитектурных решений,
Matt grill gates с глоссарием, обязательные Matt TDD slices, поддержку
архитектурных диаграмм, сценарии в стиле Gherkin, обязательные контрольные
точки Git и проверку совместимости надстройки.

OpenSpec остается движком. Codex выполняет рабочий процесс.

## Главное

- Локальная схема OpenSpec: `intent-driven`.
- Жизненный цикл: `proposal -> specs -> grill -> design -> design-review -> adr -> test-plan -> tasks -> apply -> verify -> archive`.
- Команды Codex `/opsx:*` для полного рабочего процесса OpenSpec.
- Корневой `CONSTITUTION.md` для постоянных правил проекта, которые Codex читает перед `/opsx:*`.
- Корневой `ARCHITECTURE.md` как текущий снимок архитектуры для новых чатов.
- Корневой `CONTEXT.md` как глоссарий и язык предметной области для grill/TDD flows.
- Локальный `.secrets.local.env` для доступов к внешним системам без попадания секретов в Git.
- Обязательные gates `grill.md` и `design-review.md` на основе полного Matt `grill-with-docs`.
- Сценарии в стиле Gherkin внутри Markdown-спецификаций OpenSpec.
- Легковесные архитектурные диаграммы для нетривиальных границ и интеграций.
- Обязательный gate `test-plan.md` для Matt TDD с вертикальными циклами RED/GREEN/REFACTOR.
- Проверка архитектурных решений в каждом изменении и долговременная история ADR.
- Обязательные контрольные точки Git после артефактов и групп реализации.
- Необязательные hand-off prompts Codex Goal для длинных `/opsx:apply` и подходящих `/opsx:bulk-apply`.
- Безопасная установка в новые и существующие проекты без тихой перезаписи файлов.
- Проверка совместимости после обновлений OpenSpec или самой надстройки.
- Опубликованные канонические спецификации OpenSpec, описывающие поведение шаблона.

## Текущее состояние разработки

`v0.1.3` усиливает предыдущие gates полной интеграцией Matt `grill-with-docs` и Matt `tdd`:

- корневой `CONSTITUTION.md` для обязательных правил проекта, которые Codex читает перед `/opsx:*`;
- корневой `ARCHITECTURE.md` для текущей архитектуры и ссылок на действующие ADR;
- корневой `CONTEXT.md` для глоссария и языка предметной области;
- общий skill `project-constitution` для `/opsx:*` prompts и навыков жизненного цикла OpenSpec;
- строгую обработку отсутствующей конституции с bootstrap-safe и диагностическими исключениями;
- остановку при конфликте правил конституции с запросом пользователя или артефактами OpenSpec;
- локальный `.secrets.local.env` для доступов к внешним системам и tracked `.secrets.example.env` только с placeholders;
- bridge-документ `openspec/README.md`, который объясняет, какой контекст живёт вне артефактов OpenSpec changes;
- модель снимка архитектуры на основе ADR 0003: `ARCHITECTURE.md` хранит текущее состояние, а `adr/` — долговременную историю решений;
- first-class артефакты `grill.md`, `design-review.md` и `test-plan.md` в lifecycle `intent-driven`;
- `/opsx:continue`, `/opsx:ff`, `/opsx:apply`, `/opsx:verify` и overlay smoke checks, связанные с автоматическими grill gates и обязательным TDD evidence;
- поддержку Greenfield-установки с заменой generated overlay files через `scripts/install-overlay --force-overlay`.

## Текущее состояние репозитория

Начальное изменение реализации, подсказки Codex Goal, поддержка конституции
проекта, формализация архитектуры project context, исторические gates review/test-plan
и усиленные Matt grill/TDD gates v0.1.3 уже заархивированы. В репозитории есть канонические
спецификации OpenSpec для базовой надстройки, goal-guided apply/bulk-apply,
постоянного контекста проекта, текущего набора проектных ADR и внедрённых
усиленных lifecycle gates Matt grill/TDD. Сейчас активных изменений OpenSpec нет.

| Область | Состояние |
| --- | --- |
| Активные изменения OpenSpec | Нет |
| Схема по умолчанию | `intent-driven` из `openspec/config.yaml` |
| Локальная схема проекта | `openspec/schemas/intent-driven/` |
| Канонические спецификации | `openspec/specs/**/spec.md` |
| Архив изменений | `openspec/changes/archive/2026-05-24-implement-intent-driven-codex-template/`<br>`openspec/changes/archive/2026-05-24-add-codex-goal-guidance/`<br>`openspec/changes/archive/2026-05-25-add-project-constitution/`<br>`openspec/changes/archive/2026-05-26-formalize-project-context-architecture/`<br>`openspec/changes/archive/2026-05-28-add-mandatory-review-and-tdd-gates/`<br>`openspec/changes/archive/2026-05-28-harden-mandatory-grill-and-tdd-gates/` |
| Спецификации Goal guidance | `openspec/specs/codex-opsx-workflow/spec.md`, `openspec/specs/template-installation/spec.md` |
| Контекст проекта | `CONSTITUTION.md`, `ARCHITECTURE.md`, `openspec/README.md`, `.secrets.example.env`, `.codex/skills/project-constitution/SKILL.md` |
| Долговременные ADR проекта | `adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md`, `adr/0003-formalize-project-context-entrypoints.md`, `adr/0005-adopt-matt-grill-and-tdd-gates.md` (ADR 0002 устарел и заменён ADR 0003) |
| Проверка совместимости | `scripts/check-overlay` |
| Установщик | `scripts/install-overlay` |

## Архитектура

### Контекст системы

```mermaid
flowchart LR
  Dev["Разработчик / владелец проекта"]
  Codex["Агент Codex
исполнитель /opsx:* workflow"]
  Goal["Codex Goal
необязательная оркестрация длинных прогонов"]
  Overlay["Надстройка Intent-Driven Codex
.codex prompts и skills"]
  Context["Контекст проекта
CONSTITUTION.md + CONTEXT.md + ARCHITECTURE.md + ADRs"]
  Secrets[".secrets.local.env
локальные значения только когда нужны"]
  OpenSpec["OpenSpec CLI 1.3.x
движок lifecycle"]
  Lifecycle["Intent-driven lifecycle
proposal → specs → grill → design → design-review → adr → test-plan → tasks"]
  Project["Целевой репозиторий
код, тесты, docs, OpenSpec"]
  External["Внешние системы
только если разрешены constitution"]
  UpstreamSchemas["intent-driven-dev/openspec-schemas
идеи схемы"]
  UpstreamTemplate["intent-driven-dev/intent-driven-template
идеи workflow"]

  Dev -->|"запускает /opsx:*"| Codex
  Dev -->|"может передать длинный run"| Goal
  Goal -->|"продолжает встроенный workflow"| Codex
  Codex -->|"использует"| Overlay
  Overlay -->|"проверяет перед действиями"| Context
  Overlay -. "читает только для нужного внешнего доступа" .-> Secrets
  Overlay -->|"вызывает публичные команды"| OpenSpec
  OpenSpec -->|"упорядочивает artifacts"| Lifecycle
  OpenSpec -->|"читает и пишет artifacts"| Project
  Overlay -->|"устанавливает и проверяет overlay"| Project
  Overlay -. "использует разрешённые интеграции" .-> External
  OpenSpec -. "не читает project context напрямую" .-> Context
  Overlay -.->|"адаптирует"| UpstreamSchemas
  Overlay -.->|"адаптирует"| UpstreamTemplate

  classDef actor fill:#fff4cc,stroke:#d48a00,color:#332200,stroke-width:2px;
  classDef codex fill:#dff3ff,stroke:#1d75b9,color:#042033,stroke-width:2px;
  classDef rules fill:#fff7ed,stroke:#ea580c,color:#431407,stroke-width:2px;
  classDef secret fill:#fee2e2,stroke:#dc2626,color:#450a0a,stroke-width:2px;
  classDef openspec fill:#e8fff5,stroke:#16885a,color:#06291b,stroke-width:2px;
  classDef repo fill:#e8f7e8,stroke:#2e8b57,color:#062d18,stroke-width:2px;
  classDef upstream fill:#f3e8ff,stroke:#7b3fb2,color:#26083d,stroke-width:2px;
  class Dev actor;
  class Codex,Goal,Overlay codex;
  class Context rules;
  class Secrets secret;
  class OpenSpec,Lifecycle openspec;
  class Project,External repo;
  class UpstreamSchemas,UpstreamTemplate upstream;
```

### Контейнеры и функциональность

```mermaid
flowchart TB
  User["Разработчик"]

  subgraph CodexLayer["Слой Codex (.codex)"]
    Prompts["/opsx:* prompts
new, continue, ff, apply, verify, archive"]
    ConstitutionSkill["skill project-constitution
обязательный preflight"]
    GrillGates["grill + design-review gates
grill-with-docs gates"]
    TDDGate["test-plan gate
Matt TDD slices"]
    LifecycleSkills["Навыки OpenSpec lifecycle
переходы состояния и instructions"]
    GoalGuidance["Goal guidance
copy-paste /goal hand-off"]
    GitSkill["Git discipline
checkpoint boundaries"]
  end

  subgraph ProjectContext["Постоянный контекст проекта"]
    Constitution["CONSTITUTION.md
правила проекта, MCP, docs, verification"]
    Architecture["ARCHITECTURE.md
текущий снимок архитектуры"]
    ContextDoc["CONTEXT.md
только глоссарий"]
    ADRs["adr/
долговременная история решений"]
    SecretsExample[".secrets.example.env
tracked имена переменных"]
    LocalSecrets[".secrets.local.env
ignored local values"]
  end

  subgraph OpenSpecLayer["Слой OpenSpec"]
    CLI["OpenSpec CLI 1.3.x"]
    Schema["Схема intent-driven
proposal → specs → grill → design → design-review → adr → test-plan → tasks"]
    Templates["Шаблоны artifacts
включая grill.md, design-review.md и test-plan.md"]
    ApplyContext["apply context
все завершённые planning artifacts"]
  end

  subgraph ProjectState["Состояние проекта"]
    Change["openspec/changes/<change>
proposal/specs/grill/design/design-review/adr/test-plan/tasks"]
    CanonicalSpecs["openspec/specs/**/spec.md
каноническое поведение"]
    Archive["openspec/changes/archive/**
история закрытых changes"]
    Code["реализация
исходный код, тесты, docs"]
  end

  subgraph Safety["Безопасность установки и обновления"]
    Installer["scripts/install-overlay
Brownfield-safe, Greenfield force option"]
    Checker["scripts/check-overlay
lifecycle и apply-context smoke tests"]
    Docs["README / INSTALL_CODEX / AGENTS / docs"]
  end

  User -->|"запускает workflow"| Prompts
  Prompts --> ConstitutionSkill
  LifecycleSkills --> ConstitutionSkill
  ConstitutionSkill --> Constitution
  ConstitutionSkill --> Architecture
  ConstitutionSkill --> ContextDoc
  ConstitutionSkill --> ADRs
  ConstitutionSkill -. "только имена переменных" .-> SecretsExample
  ConstitutionSkill -. "читать только когда нужно" .-> LocalSecrets
  Prompts --> LifecycleSkills
  Prompts --> GrillGates
  Prompts --> TDDGate
  Prompts --> GitSkill
  Prompts -->|"длинный или рискованный apply"| GoalGuidance
  GoalGuidance --> LifecycleSkills
  GrillGates --> Change
  TDDGate --> Change
  LifecycleSkills --> CLI
  CLI --> Schema
  Schema --> Templates
  Templates --> Change
  CLI --> ApplyContext
  ApplyContext --> Code
  CLI --> CanonicalSpecs
  CLI --> Archive
  GitSkill --> Change
  Checker --> Constitution
  Checker --> CLI
  Checker --> Schema
  Checker --> ApplyContext
  Installer --> Prompts
  Installer --> LifecycleSkills
  Installer --> Constitution
  Docs --> User

  classDef actor fill:#ffffff,stroke:#555,color:#111,stroke-width:2px;
  classDef codex fill:#e8f1ff,stroke:#2f67d8,color:#061b4a,stroke-width:2px;
  classDef rules fill:#fff7ed,stroke:#ea580c,color:#431407,stroke-width:2px;
  classDef secret fill:#fee2e2,stroke:#dc2626,color:#450a0a,stroke-width:2px;
  classDef openspec fill:#e8fff5,stroke:#16885a,color:#06291b,stroke-width:2px;
  classDef state fill:#fff4e6,stroke:#d77a00,color:#3b2000,stroke-width:2px;
  classDef safety fill:#f5e8ff,stroke:#8b42c9,color:#2a083d,stroke-width:2px;
  class User actor;
  class Prompts,ConstitutionSkill,GrillGates,TDDGate,LifecycleSkills,GoalGuidance,GitSkill codex;
  class Constitution,ContextDoc,Architecture,ADRs,SecretsExample rules;
  class LocalSecrets secret;
  class CLI,Schema,Templates,ApplyContext openspec;
  class Change,CanonicalSpecs,Archive,Code state;
  class Installer,Checker,Docs safety;
```

## Схема OpenSpec

В `openspec/config.yaml` выбрана локальная схема проекта:

```yaml
schema: intent-driven
```

Граф артефактов:

| Артефакт | Путь внутри изменения | Зависит от | Назначение |
| --- | --- | --- | --- |
| `proposal` | `proposal.md` | — | Намерение, ценность, область изменения, возможности и влияние. |
| `specs` | `specs/**/spec.md` | `proposal` | Наблюдаемое поведение как изменения требований OpenSpec и сценарии. |
| `grill` | `grill.md` | `proposal`, `specs` | Обязательный pre-design gate Matt `grill-with-docs`. |
| `design` | `design.md` | `proposal`, `specs`, `grill` | Технический подход, архитектурные границы, риски и компромиссы. |
| `design-review` | `design-review.md` | `design` | Обязательный post-design gate Matt `grill-with-docs`. |
| `adr` | `adr.md` | `design-review` | ADR review для изменения с учетом findings из grill gates. |
| `test-plan` | `test-plan.md` | `adr` | План вертикальных Matt TDD slices до задач реализации. |
| `tasks` | `tasks.md` | `specs`, `grill`, `design`, `design-review`, `adr`, `test-plan` | Упорядоченный список задач реализации. |

Реализация начинается только после завершения `grill`, `design-review`, `adr`, `test-plan` и `tasks` и фиксации состояния
планирования в Git.

## Команды

Файлы команд находятся в `.codex/prompts`.

| Команда | Назначение |
| --- | --- |
| `/opsx:explore` | Исследовать идею, проблему или участок кода без реализации. |
| `/opsx:new` | Создать новое изменение OpenSpec и показать инструкции первого артефакта. |
| `/opsx:continue` | Создать ровно один следующий готовый артефакт существующего изменения. |
| `/opsx:propose` | Быстро подготовить артефакты планирования, если пользователь явно выбрал быстрый путь. |
| `/opsx:ff` | Быстро провести подготовку артефактов с видимыми контрольными точками. |
| `/opsx:apply` | Реализовать ожидающие задачи по контексту OpenSpec; для длинных или рискованных прогонов может сначала напечатать copy-paste prompt `/goal` и остановиться до правок. |
| `/opsx:verify` | Проверить реализацию по спецификациям, проектному решению, ADR и задачам. |
| `/opsx:sync` | Синхронизировать изменения спецификаций без архивации. |
| `/opsx:archive` | Архивировать проверенное и встроенное изменение. |
| `/opsx:check-overlay` | Проверить совместимость надстройки. |
| `/opsx:bulk-apply` | Реализовать несколько независимых изменений в изолированных потоках; для подходящих multi-change прогонов может сначала напечатать родительский `/goal` до worktree/subagents. |
| `/opsx:bulk-archive` | Архивировать несколько завершенных изменений после проверки конфликтов. |

## Контекст проекта

`ARCHITECTURE.md` — текущий снимок архитектуры для новых чатов и архитектурно значимых работ. Он кратко описывает актуальное состояние и ссылается на действующие ADR. Долговременная мотивация остается в `adr/`, а `openspec/README.md` связывает файлы жизненного цикла OpenSpec с корневым контекстом проекта.

`CONSTITUTION.md` — постоянный Git-tracked источник правил проекта,
который Codex читает перед действиями `/opsx:*`. Это **не** артефакт изменения
OpenSpec: файл не архивируется вместе с changes, и сам OpenSpec CLI его не
читает. За исполнение отвечает слой Codex: общий skill
`project-constitution`, короткие preflight-правила в `/opsx:*` prompts и
навыки жизненного цикла OpenSpec.

Используйте конституцию, чтобы зафиксировать контекст, который AI не должен
упускать:

| Секция constitution | Для чего Codex ее использует |
| --- | --- |
| Required Technologies | Обязательные языки, фреймворки, рантаймы, package managers, архитектурные правила и standards. |
| MCP Servers | Какой MCP server использовать для блока разработки и какие несекретные параметры допустимы. |
| External Systems | Системы без MCP, способ доступа и имена переменных для нужных credentials. |
| Secret Handling | Граница между tracked именами переменных и локальными secret values. |
| Documentation Sources | Проектная документация, official docs и правила поиска, которым Codex должен следовать. |
| Verification Rules | Проверки, которые Codex должен выполнить или явно описать как limitation. |
| Additional AI Instructions | Проектные ограничения, которые не должны теряться между чатами. |

### Поведение workflow

Когда конституция есть, Codex применяет релевантные правила перед planning,
apply, verify, sync, archive и bulk workflows. Так обязательные технологии, MCP,
правила документации и verification остаются видимыми на всем lifecycle
OpenSpec. Для архитектурно значимых работ Codex также читает `ARCHITECTURE.md`, `adr/README.md` и релевантные действующие ADR.

Если конституция отсутствует, Codex сообщает об этом и предлагает создать файл
из шаблона. Без него можно продолжать только bootstrap-safe или диагностические
действия: exploration, создание конституции, запуск изменения для настройки конституции
или `/opsx:check-overlay`. Apply, verify, sync, archive и bulk workflows
останавливаются, пока конституция не появится или пользователь не даст явное
одноразовое исключение.

Если правила конституции конфликтуют с запросом пользователя или артефактами
OpenSpec, Codex останавливается до правок файлов и до вызовов внешних систем.
Дальше пользователь выбирает: изменить конституцию, изменить артефакт/задачу,
скорректировать запрос или дать одноразовое исключение.

### Локальные секреты

Нельзя записывать реальные логины, пароли, токены или приватные URL сервисов в
`CONSTITUTION.md`, артефакты OpenSpec, ADR, документацию, примеры, diff
или ответы в чате. Значения хранятся только в ignored `.secrets.local.env`. В
Git-tracked файлах допустимы только имена переменных и пустые placeholders,
например в `.secrets.example.env`.

Codex читает `.secrets.local.env` только когда текущему workflow действительно
нужна внешняя система, перечисленная в конституции. Если переменных не хватает,
Codex сообщает только имена отсутствующих переменных и останавливается до
внешнего вызова.

Пример tracked записи в constitution:

```text
External system: Customer OData
Required variables: CUSTOMER_ODATA_URL, CUSTOMER_ODATA_USERNAME, CUSTOMER_ODATA_PASSWORD
Values: stored locally in .secrets.local.env
```

Пример локального secret file:

```dotenv
CUSTOMER_ODATA_URL=
CUSTOMER_ODATA_USERNAME=
CUSTOMER_ODATA_PASSWORD=
```

Tracked example должен оставаться пустым или placeholder-only; реальные значения
заполняются только в ignored local file.

## Подсказки Codex Goal

OpenSpec остается источником истины даже тогда, когда исполнением управляет
Codex Goal. Goal guidance — это только оркестрационный hand-off для длинной или
рискованной реализации; он не заменяет `proposal.md`, спецификации, `grill.md`, `design.md`, `design-review.md`,
`adr.md`, `test-plan.md`, `tasks.md`, проверку или согласование контрольных точек Git.

### Когда появляется prompt `/goal`

| Workflow | Создает `/goal`, когда | Пропускает Goal guidance, когда |
| --- | --- | --- |
| `/opsx:apply <change>` | Изменение готово к apply и в нем 3+ pending tasks, важные ограничения design/ADR, несколько checkpoint boundaries, внешние зависимости, generated assets, migrations или долгая verification. | Прогон уже находится внутри active Codex Goal для того же change, пользователь просит no goal, либо работа мала и локальна. |
| `/opsx:bulk-apply <changes...>` | После проверок готовности осталось два или более executable changes. | Осталось меньше двух executable changes, прогон уже goal-guided, либо пользователь просит no goal. |

Prompt печатается после того, как известна готовность OpenSpec, и до первого
побочного эффекта реализации: до редактирования файлов при apply и до создания
worktree или запуска subagents при bulk apply.

### Как этим пользоваться

1. Запустите `/opsx:apply <change>` или `/opsx:bulk-apply <changes...>` как обычно.
2. Если Codex напечатал сгенерированный prompt `/goal`, скопируйте его целиком и
   отправьте следующим сообщением, когда хотите передать прогон под управление Codex Goal.
3. Сгенерированный Goal сначала пытается выполнить буквальный workflow `/opsx:*`.
   Если runtime не исполняет вложенные slash-команды буквально, он использует
   fallback skill `openspec-apply-change` или `openspec-bulk-apply-change` с той
   же целью.
4. Goal считается завершенным только после apply, verify, итогового отчета и
   показа checkpoint boundaries.
5. Archive, merge, push, staging/commit, destructive Git actions и необратимые
   операции по-прежнему требуют отдельного явного approval.

### Пример сгенерированного apply goal

```text
/goal Реализуй Intent-Driven OpenSpec change add-example-guidance в текущем проекте до состояния verify-ready.

Первое действие: запусти workflow `/opsx:apply add-example-guidance`. Если вложенная slash-команда не исполняется буквально, используй workflow/skill `openspec-apply-change` для этого change.

Критерии завершения: все применимые pending tasks выполнены и отмечены только после проверки; `/opsx:verify add-example-guidance` завершен без critical issues; итоговый отчет перечисляет выполненные задачи, измененные файлы, статус проверки и нерешенные предупреждения; необходимые checkpoint boundaries показаны пользователю.

Остановись без завершения goal, если planning artifacts грязные, OpenSpec state blocked/all-done, отсутствуют credentials/secrets, недоступен внешний сервис, проверки падают по причинам вне контроля Codex, артефакты противоречат друг другу, требуется design/spec/ADR decision, либо нужен archive/merge/push/destructive Git action или другое действие с отдельным approval.
```

### Пример сгенерированного bulk goal

```text
/goal Проведи Intent-Driven OpenSpec bulk apply для изменений add-a, add-b в текущем проекте.

Первое действие: запусти workflow `/opsx:bulk-apply add-a add-b`. Если вложенная slash-команда не исполняется буквально, используй workflow/skill `openspec-bulk-apply-change` с теми же изменениями.

Критерии завершения: у каждого выполненного change есть isolated worktree, apply result, `/opsx:verify <change>` result, changed-files summary, blocker summary и normalized parent report; skipped/paused/failed changes имеют причины; parent report перечисляет worktree paths, changed files, blockers и verify status.

Остановись без завершения goal, если осталось меньше двух executable changes, planning-artifact gate не пройден, OpenSpec state blocked/all-done, worktree creation failed, subagent dispatch failed, credentials/secrets или external services недоступны, checks fail вне контроля Codex, есть artifact contradictions, требуется design/spec/ADR decision, возник merge/worktree conflict, либо нужен archive/merge/push/destructive Git action или другое действие с отдельным approval.
```

### Stop conditions и ответственность

Сгенерированный Goal должен остановиться без завершения и сообщить blocker,
affected change(s), trusted state, files changed so far и recommended next user
action, если встречает dirty planning artifacts, blocked OpenSpec state,
отсутствующие credentials/secrets, недоступные services, external check failures,
artifact contradictions, необходимость design/spec/ADR decisions,
worktree/subagent failures, merge/worktree conflicts или любое действие, которому
нужно отдельное approval.

## Навыки

Навыки находятся в `.codex/skills`. Они не заменяют OpenSpec, а помогают Codex
последовательно выполнять рабочий процесс OpenSpec.

### Навыки жизненного цикла

- `openspec-new-change` — начинает новое изменение.
- `openspec-continue-change` — создает следующий готовый артефакт.
- `openspec-propose` — готовит все артефакты планирования быстрым путем.
- `openspec-ff-change` — ускоряет подготовку артефактов планирования.
- `openspec-apply-change` — реализует задачи из изменения OpenSpec.
- `openspec-verify-change` — проверяет реализацию по артефактам изменения.
- `openspec-sync-specs` — переносит изменения в канонические спецификации.
- `openspec-archive-change` — архивирует завершенное изменение.
- `openspec-bulk-apply-change` — реализует несколько независимых изменений.
- `openspec-bulk-archive-change` — архивирует несколько завершенных изменений.
- `openspec-check-overlay` — проверяет совместимость надстройки.
- `openspec-onboard` — знакомит с рабочим процессом на реальной задаче.
- `openspec-explore` — помогает исследовать задачу до реализации.

### Навыки качества и дисциплины

- `grill-with-docs` — автоматически запускается для `grill.md` и `design-review.md`; сначала читает контекст проекта, задает по одному существенному вопросу и обновляет glossary/artifacts/ADR candidates по мере фиксации решений.
- `gherkin-authoring` — улучшает сценарии и критерии приемки, сохраняя
  Markdown-спецификации OpenSpec источником истины.
- Архитектурные диаграммы — описывают архитектурные границы, зоны ответственности,
  зависимости и потоки данных.
- `architectural-decision-records` — фиксирует долговременные архитектурные
  решения и историю их замены.
- `tdd` — canonical Matt RED/GREEN/REFACTOR дисциплина для behavior-changing `/opsx:apply`.
- `openspec-git-discipline` — удерживает контрольные точки вокруг жизненного
  цикла OpenSpec.

`grill-with-docs` намеренно заменяет проверку без документов. Он сначала читает
проект, задает только вопросы, на которые нельзя ответить по доступному
контексту, и пропускает следующий gate только когда `Open Questions` = `None`
или есть явное одноразовое исключение.

## Модель ADR

Intent-Driven Codex использует двойную модель ADR:

| Тип ADR | Расположение | Назначение |
| --- | --- | --- |
| Grill-проверки изменения | `openspec/changes/<change>/grill.md` и `openspec/changes/<change>/design-review.md` | Обязательные Matt `grill-with-docs` gates до и после design. |
| Проверка ADR для изменения | `openspec/changes/<change>/adr.md` | Обязательный ADR gate с учетом findings из grill/design-review. |
| Test plan изменения | `openspec/changes/<change>/test-plan.md` | План вертикальных Matt TDD slices до tasks. |
| Долговременное ADR проекта | `adr/NNNN-kebab-title.md` | История архитектурных решений, действующая после архивации изменений. |

Текущие действующие ADR проекта:

```text
adr/0001-adopt-codex-native-intent-driven-openspec-overlay.md
adr/0003-formalize-project-context-entrypoints.md
adr/0005-adopt-matt-grill-and-tdd-gates.md
```

Правила:

- принятые долговременные ADR не переписываются;
- изменившиеся решения заменяются новыми ADR со ссылкой на прежние;
- этапы реализации, задач и проверки читают `adr/*.md` вместе с контекстом
  OpenSpec;
- проекты, которые устанавливают этот шаблон, создают собственные ADR для своих
  архитектурных решений.

## Дисциплина Git

Каждое изменение состояния жизненного цикла OpenSpec является контрольной
точкой.

| Граница | Обязательное поведение |
| --- | --- |
| Создание изменения | Показать `git status --short`; зафиксировать состояние до зависимых артефактов. |
| Каждый артефакт планирования | Зафиксировать состояние до того, как следующий артефакт начнет от него зависеть. |
| Группа задач реализации | Зафиксировать состояние после целостной и проверенной работы. |
| Проверка | Закоммитить изменения проверки, если она изменила файлы. |
| Архивация | Закоммитить перенос в архив и синхронизацию канонических спецификаций. |

Codex не должен выполнять `git add`, `git commit`, слияние, публикацию или
архивацию без явного согласия пользователя. Одноразовое исключение должно
указать пропускаемый барьер, доверенное грязное состояние, принятый риск и
следующую контрольную точку.

## Установка

### Новый проект

```bash
cd /path/to/new-project
openspec init . --tools codex --profile core

/path/to/intent-driven-codex/scripts/install-overlay /path/to/new-project

cd /path/to/new-project
openspec schema validate intent-driven
scripts/check-overlay
```

### Существующий проект

Сначала изучите текущее состояние проекта:

```bash
git status --short
find openspec -maxdepth 3 -type f 2>/dev/null | sort
find .codex -maxdepth 3 -type f 2>/dev/null | sort
find adr -maxdepth 2 -type f 2>/dev/null | sort
```

Затем установите надстройку:

```bash
/path/to/intent-driven-codex/scripts/install-overlay /path/to/brownfield-project
```

По умолчанию установщик ничего не перезаписывает. Он сохраняет существующие
активные изменения, канонические спецификации, историю ADR, настройки `.codex`,
исходный код, тесты и проектную документацию, если пользователь явно не решил
иначе.

Подробная инструкция: [`INSTALL_CODEX.md`](INSTALL_CODEX.md).

## Структура репозитория

```text
.codex/
  prompts/                         команды Codex /opsx:*
  skills/                          навыки жизненного цикла, качества и конституции
adr/
  README.md                        правила и указатель ADR
  0001-*.md, 0002-*.md, 0003-*.md   архитектурные решения проекта
openspec/
  config.yaml                      выбирает schema: intent-driven
  schemas/intent-driven/           локальная схема и шаблоны
  specs/                           канонические спецификации после архивации
  README.md                         bridge к корневому контексту проекта
  changes/archive/                 архивированные изменения реализации
docs/
  lifecycle.md                     краткая справка по жизненному циклу
  update-safety.md                 границы безопасного обновления OpenSpec
scripts/
  check-overlay                    проверка совместимости
  install-overlay                  безопасная установка без перезаписи
CONSTITUTION.md                     Git-tracked правила проекта для preflight Codex
ARCHITECTURE.md                     текущий снимок архитектуры
.secrets.example.env               tracked пример имен переменных, без значений
README.md                          основной README на английском
README.ru.md                       русский перевод
VERSION                            версия выпуска
LICENSE                            лицензия MIT
```

## Проверка

Выполняйте эти проверки после установки или перед публикацией выпуска:

```bash
openspec schemas --json
openspec validate --all --strict
openspec schema validate intent-driven
scripts/check-overlay
```

Ожидаемый результат:

- `intent-driven` указан как локальная схема проекта;
- все канонические спецификации проходят строгую проверку;
- схема успешно проверяется;
- проверка совместимости создает, проверяет и удаляет временное изменение
  `zz-smoke-intent-overlay-*`.
- требования goal guidance остаются в канонических спецификациях и описаны в
  обоих README;
- правила конституции проекта описаны, а локальные файлы секретов остаются
  ignored/untracked.

## Версия

Текущий выпуск: `v0.1.3`.

## Лицензия

MIT. См. [`LICENSE`](LICENSE).
