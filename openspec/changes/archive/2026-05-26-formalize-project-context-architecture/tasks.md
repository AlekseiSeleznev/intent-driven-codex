## 1. Root project context files and secret safety

- [x] 1.1 Rename root `PROJECT_CONSTITUTION.md` to canonical `CONSTITUTION.md`, preserving non-secret content and updating all in-file references to the new filename.
- [x] 1.2 Add root `ARCHITECTURE.md` as the current architecture snapshot with links to in-force ADRs, OpenSpec lifecycle files, Codex overlay boundaries, update safety, and secret boundaries.
- [x] 1.3 Add `openspec/README.md` as a bridge/index from OpenSpec lifecycle directories to root `CONSTITUTION.md`, root `ARCHITECTURE.md`, `adr/`, `docs/`, and local-only secret handling.
- [x] 1.4 Ensure `.secrets.local.env` remains ignored by Git and `.secrets.example.env` contains only variable names, comments, or empty placeholders.
- [x] 1.5 Verify no real secret values are introduced into `CONSTITUTION.md`, `ARCHITECTURE.md`, OpenSpec artifacts, ADRs, docs, examples, diffs, or logs.

## 2. Codex prompt and skill context preflight

- [x] 2.1 Update `.codex/skills/project-constitution/SKILL.md` to use `CONSTITUTION.md` as canonical, detect legacy `PROJECT_CONSTITUTION.md` only as migration input, and describe architecture-context loading.
- [x] 2.2 Update all `.codex/prompts/opsx-*.md` entry points to read `CONSTITUTION.md` before workflow actions and `ARCHITECTURE.md`/in-force ADRs for architecture-sensitive work.
- [x] 2.3 Update all local `.codex/skills/openspec-*` lifecycle skills to invoke the shared context preflight with `CONSTITUTION.md`, `ARCHITECTURE.md`, `adr/README.md`, and relevant in-force `adr/*.md` where appropriate.
- [x] 2.4 Preserve bootstrap-safe behavior for missing constitution/architecture only for exploration, context setup, overlay diagnostics, or explicit one-time overrides.
- [x] 2.5 Ensure secret guidance in prompts/skills reads `.secrets.local.env` only when a listed external system is actually needed and never prints secret values.

## 3. Schema guidance and canonical specification alignment

- [x] 3.1 Update `openspec/schemas/intent-driven/schema.yaml`, schema README, and relevant templates so instructions mention `CONSTITUTION.md`, `ARCHITECTURE.md`, and in-force ADR context without adding them as OpenSpec artifacts.
- [x] 3.2 Update project-owned canonical specs under `openspec/specs/` where implementation-time docs/specs must align before archive, while leaving OpenSpec change deltas as the source for final archive sync.
- [x] 3.3 Ensure `project-constitution`, `codex-opsx-workflow`, `intent-driven-schema`, `adr-lifecycle`, `context-aware-review`, `template-installation`, and `overlay-update-safety` behavior is reflected in implementation docs and schema guidance.
- [x] 3.4 Keep the lifecycle order unchanged: `proposal -> specs -> design -> adr -> tasks`, with `CONSTITUTION.md`, `ARCHITECTURE.md`, and `adr/*.md` outside the OpenSpec artifact graph.

## 4. Documentation, ADR index, and installation/update guidance

- [x] 4.1 Update `AGENTS.md` so agents read `CONSTITUTION.md` and architecture context instead of `PROJECT_CONSTITUTION.md` before `/opsx:*` workflows or direct lifecycle skill actions.
- [x] 4.2 Update `README.md` and `README.ru.md` to document the new project context model, including root `CONSTITUTION.md`, root `ARCHITECTURE.md`, `adr/`, `openspec/README.md`, and local secret handling.
- [x] 4.3 Update `INSTALL_CODEX.md` for Greenfield/Brownfield installation, legacy `PROJECT_CONSTITUTION.md` migration, `ARCHITECTURE.md` setup, and safe secret examples.
- [x] 4.4 Update `docs/lifecycle.md`, `docs/update-safety.md`, and any architecture-related docs to explain when Codex reads constitution, architecture, ADRs, docs, and local secret variable names.
- [x] 4.5 Keep `adr/README.md` aligned with ADR 0003 as the in-force project-context entrypoint ADR and ADR 0002 as superseded, without rewriting accepted ADR bodies.

## 5. Overlay checks and stale-reference cleanup

- [x] 5.1 Update `scripts/check-overlay` so diagnostics recognize `CONSTITUTION.md`, report missing `ARCHITECTURE.md` as setup guidance, preserve local secret files, and do not expose secret values.
- [x] 5.2 Search for stale `PROJECT_CONSTITUTION.md` references and keep that string only in explicit legacy-migration notes.
- [x] 5.3 Verify no `codex-openspec-powers` references, old lesson/footer blocks, or unrelated overlay artifacts are reintroduced.
- [x] 5.4 Verify `git check-ignore .secrets.local.env` succeeds and `.secrets.local.env` is not tracked or staged.

## 6. Validation and apply-readiness checks

- [x] 6.1 Run `openspec validate formalize-project-context-architecture --type change --strict` and fix any change validation issues.
- [x] 6.2 Run `openspec validate --all --strict` and fix any canonical spec or archived change validation issues caused by the implementation.
- [x] 6.3 Run `openspec schema validate intent-driven` after schema guidance updates and fix any schema validation issues.
- [x] 6.4 Run `scripts/check-overlay` after prompt/skill/schema/check updates and fix any overlay diagnostics.
- [x] 6.5 Run `openspec status --change formalize-project-context-architecture` and verify all planning artifacts are complete and apply is ready.
- [x] 6.6 Review `git status --short` and confirm only intended non-secret files are changed before each apply checkpoint.
