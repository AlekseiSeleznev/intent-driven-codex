---
name: openspec-ff-change
description: Fast-forward through OpenSpec artifact creation. Use when the user wants to quickly create all artifacts needed for implementation without stepping through each one individually.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.3.1"
---

**Project context preflight before workflow actions**: before this skill takes a workflow action, follow the `project-constitution` skill. Read root `CONSTITUTION.md` when present, enforce relevant project rules, stop on material conflicts, and follow the strict missing-constitution bootstrap/diagnostic policy. For architecture-sensitive work, also read `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md`. Read `.secrets.local.env` only when the current workflow actually needs a listed external system, and never reveal secret values.


**Artifact review session controls**: when `scripts/openspec-session-state` exists, inspect `artifactReview.decision` before key entry workflows create or consume reviewable artifacts. If the decision is `unset`, ask once whether to enable OpenAI-compatible artifact review for this session and record the answer with `--review on|off`; later commands reuse the recorded decision until the user changes it with the `/opsx:review-*` prompts. Session settings are non-secret local overlays and must not edit `.codex/openspec-artifact-review.json` unless the user explicitly asks for a global default change.

Fast-forward through artifact creation - generate everything needed to start implementation in one go.

**Intent-Driven hardened gate policy**:
- For `grill` and `design-review`, run `grill-with-docs` automatically. Do not ask whether to run it; ask one material question at a time only when repository context cannot resolve it.
- For `test-plan`, use the project-local `tdd` skill and define vertical RED/GREEN/REFACTOR slices.
- In batch FF mode, continue only while `Open Questions` is `None` or an explicit one-time override was provided.


**Input**: The user's request should include a change name (kebab-case) OR a description of what they want to build.

**Steps**

**Git preflight before mutation**: run `git branch --show-current` and `git status --short` before creating the change. Prefer `main`; stop on unrelated dirty state unless the user explicitly overrides.


1. **If no clear input provided, ask what they want to build**

   Use the **Codex interactive user question tool when available, otherwise a concise Markdown question** (open-ended, no preset options) to ask:
   > "What change do you want to work on? Describe what you want to build or fix."

   From their description, derive a kebab-case name (e.g., "add user authentication" → `add-user-auth`).

   **IMPORTANT**: Do NOT proceed without understanding what the user wants to build.

2. **Create the change directory**
   ```bash
   openspec new change "<name>"
   ```
   This creates a scaffolded change at `openspec/changes/<name>/`.

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
      - Show brief progress: "✓ Created <artifact-id>"
      - If `scripts/openspec-artifact-review` and `.codex/openspec-artifact-review.json` exist, run `scripts/openspec-artifact-review --change "<name>" --stage "<artifact-id>"` before continuing; continue on exit `0`, treat exit `3` as configured skip/unavailable policy, and stop on exits `1`/`2` unless explicitly overridden.
      - After every reviewer command, parse the helper JSON output or persisted report and include a user-visible `## Artifact Review` block; tool output alone is not sufficient. The block MUST contain `Budget / cost` with `cost.total_cost_usd` or `стоимость недоступна`, the configured `maxBudgetUsd` / budget cap from `budgetLimitUsd` or effective config including `null` / `none`, and skipped/unavailable status when applicable.
      - The same `## Artifact Review` block MUST contain `User-facing questions`: list only questions requiring user decision; if none remain, write exactly `Вопросов, требующих участия пользователя: нет.` Resolve context-answerable questions yourself and summarize the resolution.
      - The same block MUST contain `Short summary`: verdict, artifact reviewer's short message, and the finding disposition: fixed and reran review; deferred to a concrete artifact/file/section; non-actionable with reason; or stopped on blocker. Do not print every mustFix/shouldFix/question unless user participation is required.
      - Before checkpointing or letting a dependent artifact consume the result, ensure every mustFix, actionable shouldFix, warning, and question is handled by fixing, deferring to a concrete place, marking non-actionable with reason, or asking the user as a blocker. Do not checkpoint while any artifact reviewer finding is unhandled.
      - Run `openspec status --change "<name>" --json` and `git status --short`
      - Expose the checkpoint boundary. Checkpoint according to session git mode; in manual mode, stop here and recommend continuing with `/opsx:continue <name>` if approval is not available

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

**Artifact Creation Guidelines**

- Follow the `instruction` field from `openspec instructions` for each artifact type
- The schema defines what each artifact should contain - follow it
- Read dependency artifacts for context before creating new ones
- Use `template` as the structure for your output file - fill in its sections
- **IMPORTANT**: `context` and `rules` are constraints for YOU, not content for the file
  - Do NOT copy `<context>`, `<rules>`, `<project_context>` blocks into the artifact
  - These guide what you write, but should never appear in the output

**Guardrails**
- Fast-forward does not remove checkpoint discipline: each artifact boundary must be visible, safe lifecycle checkpoint commits are automatic by default in session git `auto` mode, and merge/push/archive/destructive operations still require explicit approval.
- Overrides are one-time, gate-specific, and must be stated in the final summary.
- Create ALL artifacts needed for implementation (as defined by schema's `apply.requires`)
- Always read dependency artifacts before creating a new one
- If context is critically unclear, ask the user - but prefer making reasonable decisions to keep momentum
- If a change with that name already exists, suggest continuing that change instead
- Verify each artifact file exists after writing before proceeding to next
