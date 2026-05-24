---
name: openspec-git-discipline
description: Use when OpenSpec propose, continue, apply, verify, archive, branch, merge, or worktree timing affects git history.
license: MIT
compatibility: Codex lazy-loaded skill for git-backed OpenSpec projects.
---

# OpenSpec Git Discipline

Use this skill whenever OpenSpec lifecycle state changes or git timing affects the workflow.

Project policy: checkpoint discipline is mandatory. Every completed artifact or task group must be staged and committed after explicit user approval before the next lifecycle phase depends on it.

Core rule: every OpenSpec state change must cross `main` before the next
lifecycle phase depends on it.

- Proposal artifacts may be drafted on a branch, but must be committed and
  merged to `main` before apply starts.
- Apply may run on `main`, a branch, or a worktree only if that exact proposal
  state is already available on `main`.
- Archive should run from `main` after implementation is merged back.
- Never create commits, branches, merges, or archives unless the user explicitly
  asks.

## Gates

- Before propose: prefer `main`; otherwise warn and ask whether to continue.
- During continue: ask about committing completed artifact changes before the
  next artifact when the change is git-sensitive.
- Before apply: run `git status --short` and verify proposal artifacts are not
  only local uncommitted state.
- Before archive: run `git branch --show-current` and `git status --short`;
  stop if not on `main` or implementation has not been merged.
- After archive: ask the user whether to commit archive/spec sync changes.

## Required Language

If proposal state has not reached `main`:

> I should not apply this yet because the proposal change has not reached
> `main`. A proposal can be drafted on a branch, but apply must start only after
> that proposal state is available on `main`.

If archive is not running from merged `main`:

> I should not archive this yet because archive must run from `main` after
> implementation is merged back. Verify makes a change eligible to merge; it
> does not replace the merge.


## Mandatory Checkpoint Boundaries

Checkpoint after each of these boundaries before a later lifecycle phase depends on the result:

1. `openspec new change` creates a change directory.
2. Each planning artifact is created or materially updated: proposal, specs, design, adr, tasks.
3. Each coherent apply task group is completed and verified.
4. Verify produces an archive-ready assessment.
5. Archive moves the change and/or syncs specs.

At every checkpoint:

- run `git status --short`;
- summarize changed files;
- ask for explicit approval before `git add`, `git commit`, branch, merge, push, or archive;
- after commit, show the commit hash and re-check status.

## Override Policy

Overrides are allowed only when the user explicitly names or clearly confirms the gate being bypassed. An override is one-time and does not disable future gates. Record the override in the response with:

- gate bypassed;
- files/state trusted despite the gate;
- risk accepted;
- next checkpoint where normal discipline resumes.
