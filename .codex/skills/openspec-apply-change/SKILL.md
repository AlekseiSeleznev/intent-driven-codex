---
name: openspec-apply-change
description: Implement tasks from an OpenSpec change. Use when the user wants to start implementing, continue implementation, or work through tasks.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.3.1"
---

**Project context preflight before workflow actions**: before this skill takes a workflow action, follow the `project-constitution` skill. Read root `CONSTITUTION.md` when present, enforce relevant project rules, stop on material conflicts, and follow the strict missing-constitution bootstrap/diagnostic policy. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`. Read `.secrets.local.env` only when the current workflow actually needs a listed external system, and never reveal secret values.


Implement tasks from an OpenSpec change.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes and use the **Codex interactive user question tool when available, otherwise a concise Markdown question** to let the user select

   Always announce: "Using change: <name>" and how to override (e.g., `/opsx:apply <other>`).

2. **Check status to understand the schema**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to understand:
   - `schemaName`: The workflow being used (e.g., "spec-driven")
   - Which artifact contains the tasks (typically "tasks" for spec-driven, check status for others)

3. **Git hard gate before apply**

   Run:
   ```bash
   git status --short
   openspec status --change "<name>" --json
   ```

   Refuse to implement if planning artifacts under `openspec/changes/<name>/` are missing or uncommitted (`proposal.md`, `specs/**`, `grill.md`, `design.md`, `design-review.md`, `adr.md`, `test-plan.md`, `tasks.md`) unless the user gives an explicit one-time override naming the files/gate.

4. **Get apply instructions**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   This returns:
   - `contextFiles`: artifact ID -> array of concrete file paths (varies by schema - could be proposal/specs/grill/design/design-review/adr/test-plan/tasks or spec/tests/implementation/docs)
   - Progress (total, complete, remaining)
   - Task list with status
   - Dynamic instruction based on current state

   **Handle states:**
   - If `state: "blocked"` (missing artifacts): show message, suggest using openspec-continue-change
   - If `state: "all_done"`: congratulate, suggest archive
   - Otherwise: proceed to implementation

5. **Read context files**

   Read every file path listed under `contextFiles` from the apply instructions output.
   The files depend on the schema being used:
   - **spec-driven**: proposal, specs, design, tasks
   - **intent-driven**: proposal, specs, grill, design, design-review, per-change adr, test-plan, tasks; then read root `CONTEXT.md`/`CONTEXT-MAP.md` when present, top-level `adr/*.md` when present, and derive the in-force ADR set by following supersession links
   - Other schemas: follow the contextFiles from CLI output

   For intent-driven changes, implementation must obey grill and design-review resolutions, the per-change ADR review, Matt TDD test-plan ordering/evidence, and the in-force durable ADR set.

6. **Show current progress**

   Display:
   - Schema being used
   - Progress: "N/M tasks complete"
   - Remaining tasks overview
   - Dynamic instruction from CLI

7. **Goal-guidance preflight before implementation**

   Run this preflight after apply eligibility and context are known, but before
   implementation file edits.

   **Skip goal generation and continue normal apply when:**
   - The current session is already inside an active Codex Goal whose scope includes `<name>` (avoid nested goals).
   - The user explicitly asked to continue without a goal, said "no goal", or otherwise clearly bypassed goal guidance.
   - The apply work is small, local, and unlikely to need multiple checkpoint boundaries.

   **Do not generate a goal prompt when:**
   - The git/planning-artifact gate failed or planning artifacts are dirty.
   - `openspec instructions apply` returned `state: "blocked"` or `state: "all_done"`.
   - Context artifacts conflict so implementation should pause for artifact updates.

   **Generate a copy-paste `/goal` prompt and stop before implementation when
   any of these deterministic conditions apply:**
   - Three or more pending tasks remain.
   - The work is expected to cross multiple checkpoint boundaries.
   - The change has material design or ADR constraints.
   - The change touches multiple capabilities, subsystems, or documentation plus code.
   - The flow depends on external services, credentials, generated assets, migrations, or long-running verification.
   - The user asks Codex to keep going, implement everything, or avoid stopping except for blockers.

   **Generated apply goal prompt shape** (localize prose to the user's language,
   but keep command, change, and skill names exact):

   ```text
   /goal Реализуй Intent-Driven OpenSpec change <change> в текущем проекте до состояния verify-ready.

   Первое действие: запусти workflow `/opsx:apply <change>`. Если вложенная slash-команда не исполняется буквально, используй workflow/skill `openspec-apply-change` для этого change.

   Критерии завершения: все применимые pending tasks выполнены и отмечены только после проверки; `/opsx:verify <change>` завершен без critical issues; итоговый отчет перечисляет выполненные задачи, измененные файлы, статус проверки и нерешенные предупреждения; необходимые checkpoint boundaries показаны пользователю.

   Остановись без завершения goal, если planning artifacts грязные, grill/design-review/test-plan отсутствуют или грязные, OpenSpec state blocked/all-done, отсутствуют credentials/secrets, недоступен внешний сервис, проверки падают по причинам вне контроля Codex, артефакты противоречат друг другу, требуется design/spec/ADR decision, либо нужен archive/merge/push/destructive git action или другое действие с отдельным approval. В отчете укажи blocker, trusted state, files changed so far и recommended next user action.
   ```

8. **Implement tasks (loop until done or blocked)**

   For each pending task or coherent task group:
   - Show which task is being worked on
   - For behavior-changing work, use the project-local `tdd` skill automatically: one vertical RED -> GREEN -> REFACTOR slice at a time
   - Observe and record RED evidence before production code unless `test-plan.md` has an explicit approved exception
   - Make the minimum code changes required for GREEN
   - Run the GREEN check and relevant surrounding checks
   - Keep changes minimal and focused; refactor only while GREEN
   - Mark task complete in the tasks file: `- [ ]` → `- [x]` only after RED/GREEN evidence or an approved exception
   - Continue to next task

   **Pause if:**
   - Task is unclear → ask for clarification
   - Implementation reveals a design issue → suggest updating artifacts
   - Error or blocker encountered → report and wait for guidance
   - User interrupts

9. **On completion or pause, show status**

   Display:
   - Tasks completed this session
   - Overall progress: "N/M tasks complete"
   - If all done: suggest archive
   - If paused: explain why and wait for guidance

**Output During Implementation**

```
## Implementing: <change-name> (schema: <schema-name>)

Working on task 3/7: <task description>
[...implementation happening...]
✓ Task complete

Working on task 4/7: <task description>
[...implementation happening...]
✓ Task complete
```

**Output On Goal Hand-Off**

```
## Goal Guidance Recommended

**Change:** <change-name>
**Schema:** <schema-name>
**Reason:** <deterministic condition, e.g. 12 pending tasks and ADR constraints>

Copy and send this as the next message if you want Codex Goal to manage the run:

<generated /goal prompt>

Stopping before implementation file edits.
```

**Output On Completion**

```
## Implementation Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 7/7 tasks complete ✓

### Completed This Session
- [x] Task 1
- [x] Task 2
...

All tasks complete! Ready to archive this change.
```

**Output On Pause (Issue Encountered)**

```
## Implementation Paused

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 4/7 tasks complete

### Issue Encountered
<description of the issue>

**Options:**
1. <option 1>
2. <option 2>
3. Other approach

What would you like to do?
```

**Guardrails**
- Do not implement from uncommitted planning artifacts unless the user explicitly overrides the planning gate.
- Do not create nested goals; when already inside an active Codex Goal for the change, continue apply directly.
- When goal guidance is triggered, output the generated `/goal` prompt and stop before implementation file edits.
- After each coherent task group, show `git status --short` and request checkpoint approval; do not stage/commit without explicit user approval.
- Keep going through tasks until done or blocked
- Always read context files before starting (from the apply instructions output)
- If task is ambiguous, pause and ask before implementing
- If implementation reveals issues, pause and suggest artifact updates
- Keep code changes minimal and scoped to each task
- Update task checkbox immediately after completing each task
- Pause on errors, blockers, or unclear requirements - don't guess
- Use contextFiles from CLI output, don't assume specific file names

**Fluid Workflow Integration**

This skill supports the "actions on a change" model:

- **Can be invoked anytime**: Before all artifacts are done (if tasks exist), after partial implementation, interleaved with other actions
- **Allows artifact updates**: If implementation reveals design issues, suggest updating artifacts - not phase-locked, work fluidly
