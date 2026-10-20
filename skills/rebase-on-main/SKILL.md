---
name: rebase-on-main
description: Rebase the current Git branch onto the repository mainline branch and resolve merge conflicts carefully.
disable-model-invocation: true
---

# Rebase On Main

Use when the user asks to rebase on main/master, update a branch from mainline or resolve rebase conflicts.
With tricky or unclear conflicts ask the user for judgement and decision on how to resolve them.

## Workflow

1. Inspect the repository before changing history:
   - Run `git status --short --branch`.
   - Confirm the current branch is not `main`, `master`, or a detached `HEAD`.
   - If unrelated local changes exist, do not stash or discard them silently. Ask whether to include, stash, or stop.
2. Identify the mainline branch:
   - Prefer the upstream default branch: `git symbolic-ref --short refs/remotes/origin/HEAD`.
   - Fall back to `origin/main`, then `origin/master`, then local `main` or `master`.
   - If the repository uses a different default branch and it is not obvious, ask.
3. Refresh refs with `git fetch --prune origin` when a remote exists.
4. Start the rebase with `git rebase <mainline-ref>`.
5. Resolve conflicts one stop at a time:
   - Run `git status --short` and inspect each conflicted file.
   - Read the surrounding code before editing; prefer the repository's current mainline shape unless the branch's commit clearly carries intentional behavior.
   - Resolve by preserving both sides when they touch independent behavior.
   - For generated lockfiles or schema snapshots, use the repo-native generator or package manager when available; otherwise resolve minimally and validate.
   - For deletion/rename conflicts, inspect history with `git log --oneline --follow -- <path>` or `git diff --name-status` before choosing.
   - If a conflict encodes a product decision, ambiguous data migration, public API change, or security-sensitive behavior or is otherwise unclear, ask the user before continuing.
6. After each resolution:
   - Stage only resolved files with `git add <path>`.
   - Continue with `git rebase --continue`.
   - Repeat until complete.
7. Validate after the rebase:
   - Run the smallest meaningful project checks available: targeted tests, typecheck, build, or lint. Prefer commands documented in the repo.
   - If checks are expensive or unavailable, say what was skipped and why.
8. Stop before publishing:
   - Show final `git status --short --branch` and summarize conflicts resolved and checks run.
   - Do not run `git push --force-with-lease`; leave that command to the user unless they explicitly ask.

## Safety Rules

- Never run destructive cleanup commands such as `git reset --hard`, `git clean`, `git restore`, or file deletion unless the user explicitly asks.
- Never switch branches without user consent.
- Never amend, squash, reorder, or drop commits unless the user asks.
- If the rebase becomes semantically risky, pause and ask a short question with the concrete file and conflict.
- If aborting is the safest path, explain why and ask before `git rebase --abort`.

## Useful Commands

```bash
git status --short --branch
git symbolic-ref --short refs/remotes/origin/HEAD
git fetch --prune origin
git rebase origin/main
git status --short
git diff --check
git rebase --continue
```
