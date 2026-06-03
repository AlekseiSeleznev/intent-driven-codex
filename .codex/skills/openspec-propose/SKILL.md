---
name: openspec-propose
description: Propose a new change with all artifacts generated in one step. Use when the user wants to quickly describe what they want to build and get a complete proposal with design, specs, and tasks ready for implementation.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.3.1"
---

**Project context preflight before workflow actions**: before this skill takes a workflow action, follow the `project-constitution` skill. Read root `CONSTITUTION.md` when present, enforce relevant project rules, stop on material conflicts, and follow the strict missing-constitution bootstrap/diagnostic policy. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`. Read `.secrets.local.env` only when the current workflow actually needs a listed external system, and never reveal secret values.


**Artifact review session controls**: when `scripts/openspec-session-state` exists, inspect `artifactReview.decision` before key entry workflows create or consume reviewable artifacts. If the decision is `unset`, ask once whether to enable OpenAI-compatible artifact review for this session and record the answer with `--review on|off`; later commands reuse the recorded decision until the user changes it with the `/opsx:review-*` prompts. Session settings are non-secret local overlays and must not edit `.codex/openspec-artifact-review.json` unless the user explicitly asks for a global default change.

Propose a new change - create the change and generate all artifacts in one step.

I'll create a change with artifacts as defined by the active schema. For the bundled intent-driven schema this is:
- proposal.md (intent, value, scope, capabilities)
- specs/*/spec.md (observable behavior and Gherkin-style scenarios)
- grill.md (mandatory Matt grill-with-docs pre-design gate)
- design.md (technical approach and boundary diagrams when useful)
- design-review.md (mandatory Matt grill-with-docs post-design gate)
- adr.md (per-change ADR review, with optional durable top-level ADRs)
- test-plan.md (Matt TDD vertical slice plan)
- tasks.md (implementation steps)

When ready to implement, run /opsx:apply

---

**Input**: The user's request should include a change name (kebab-case) OR a description of what they want to build.

**Steps**

**Git preflight before mutation**: run `git branch --show-current` and `git status --short` before creating or changing OpenSpec files. Prefer `main`; if unrelated changes are present, stop unless the user explicitly includes them or asks to override.


1. **If no clear input provided, ask what they want to build**

   Use the **Codex interactive user question tool when available, otherwise a concise Markdown question** (open-ended, no preset options) to ask:
   > "What change do you want to work on? Describe what you want to build or fix."

   From their description, derive a kebab-case name (e.g., "add user authentication" → `add-user-auth`).

   **IMPORTANT**: Do NOT proceed without understanding what the user wants to build.

2. **Create the change directory**
   ```bash
   openspec new change "<name>"
   ```
   This creates a scaffolded change at `openspec/changes/<name>/` with `.openspec.yaml`.

3. **Get the artifact build order**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to get:
   - `applyRequires`: array of artifact IDs needed before implementation (e.g., `["tasks"]`)
   - `artifacts`: list of all artifacts with their status and dependencies

4. **Create artifacts in sequence until apply-ready**

   Use the **Codex task plan tool when available** to track progress through the artifacts.

   Loop through artifacts in dependency order (artifacts with no pending dependencies first):

   a. **For each artifact that is `ready` (dependencies satisfied)**:
      - Get instructions:
        ```bash
        openspec instructions <artifact-id> --change "<name>" --json
        ```
      - The instructions JSON includes:
        - `context`: Project background (constraints for you - do NOT include in output)
        - `rules`: Artifact-specific rules (constraints for you - do NOT include in output)
        - `template`: The structure to use for your output file
        - `instruction`: Schema-specific guidance for this artifact type
        - `outputPath`: Where to write the artifact
        - `dependencies`: Completed artifacts to read for context
      - Read any completed dependency files for context
      - Create the artifact file using `template` as the structure
      - Apply `context` and `rules` as constraints - but do NOT copy them into the file
      - Show brief progress: "Created <artifact-id>"
      - If `scripts/openspec-artifact-review` and `.codex/openspec-artifact-review.json` exist, run `scripts/openspec-artifact-review --change "<name>" --stage "<artifact-id>"` before the next artifact consumes this one; continue on exit `0`, treat exit `3` as configured skip/unavailable policy, and stop on exits `1`/`2` unless explicitly overridden.
      - After every reviewer command, parse the helper JSON output or persisted report and include a user-visible `## Artifact Review` block; tool output alone is not sufficient. The block MUST contain `Budget / cost` with `cost.total_cost_usd` or `стоимость недоступна`, the configured `maxBudgetUsd` / budget cap from `budgetLimitUsd` or effective config including `null` / `none`, and skipped/unavailable status when applicable.
      - The same `## Artifact Review` block MUST contain `User-facing questions`: list only questions requiring user decision; if none remain, write exactly `Вопросов, требующих участия пользователя: нет.` Resolve context-answerable questions yourself and summarize the resolution.
      - The same block MUST contain `Short summary`: verdict, artifact reviewer's short message, and the finding disposition: fixed and reran review; deferred to a concrete artifact/file/section; non-actionable with reason; or stopped on blocker. Do not print every mustFix/shouldFix/question unless user participation is required.
      - Before checkpointing or letting a dependent artifact consume the result, ensure every mustFix, actionable shouldFix, warning, and question is handled by fixing, deferring to a concrete place, marking non-actionable with reason, or asking the user as a blocker. Do not checkpoint while any artifact reviewer finding is unhandled.
      - Run `openspec status --change "<name>" --json` and `git status --short`
      - Treat this artifact as a checkpoint boundary: in automatic mode, create a scoped checkpoint after showing paths/message/hash; in manual mode, request approval before staging/committing or before a dependent artifact consumes uncommitted state

   b. **Continue until all `applyRequires` artifacts are complete**
      - After creating each artifact, re-run `openspec status --change "<name>" --json`
      - Check if every artifact ID in `applyRequires` has `status: "done"` in the artifacts array
      - Stop when all `applyRequires` artifacts are done

   c. **If an artifact requires user input** (unclear context):
      - Use **Codex interactive user question tool when available, otherwise a concise Markdown question** to clarify
      - Then continue with creation

5. **Show final status**
   ```bash
   openspec status --change "<name>"
   ```

**Output**

After completing all artifacts, summarize:
- Change name and location
- List of artifacts created with brief descriptions
- What's ready: "All artifacts created! Ready for implementation."
- Prompt: "Run `/opsx:apply` or ask me to implement to start working on the tasks."

**Intent-Driven Support Skill Policy**

- Run `grill-with-docs` automatically for `grill.md` and `design-review.md`. Do not ask whether to run it; ask one material question at a time only when repository context cannot resolve it.
- Use `gherkin-authoring` when drafting or improving OpenSpec scenario content; keep OpenSpec Markdown as the source of truth.
- Use `c4-diagrams` for non-trivial system boundaries, runtime containers, integrations, or data flow; otherwise include a clear no-diagram rationale.
- Use `architectural-decision-records` for ADR review and durable ADR drafting; never rewrite accepted ADRs, supersede them append-only.
- When creating `test-plan.md`, use the project-local `tdd` skill and plan vertical RED/GREEN/REFACTOR slices. When creating `tasks.md`, read proposal/specs/grill/design/design-review/per-change `adr.md`/`test-plan.md` and top-level `adr/*.md`; tasks must preserve in-force ADR and TDD constraints.

**Artifact Creation Guidelines**

- Follow the `instruction` field from `openspec instructions` for each artifact type
- The schema defines what each artifact should contain - follow it
- Read dependency artifacts for context before creating new ones
- Use `template` as the structure for your output file - fill in its sections
- **IMPORTANT**: `context` and `rules` are constraints for YOU, not content for the file
  - Do NOT copy `<context>`, `<rules>`, `<project_context>` blocks into the artifact
  - These guide what you write, but should never appear in the output

**Guardrails**
- Checkpoint after each artifact. Safe lifecycle checkpoint commits are automatic by default in session git `auto` mode; merge, push, archive, destructive operations, and hard-gate bypasses still require explicit approval.
- A user override must name what is being bypassed, applies only to the current gate, and must be repeated in the summary.
- Create ALL artifacts needed for implementation (as defined by schema's `apply.requires`)
- Always read dependency artifacts before creating a new one
- If context is critically unclear, ask the user - but prefer making reasonable decisions to keep momentum
- If a change with that name already exists, ask if user wants to continue it or create a new one
- Verify each artifact file exists after writing before proceeding to next
