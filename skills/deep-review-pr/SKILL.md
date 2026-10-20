---
name: deep-review-pr
description: Deep code review - bugs, branch changes, uncommitted changes, best fix, stale-or-real, read code first. Use only when explictly asked to do deep-review-pr.
---

# Deep Review PR

Use when reviewing the current branch or worktree as a PR into the mainline branch (master/main), comparing any branch/range against mainline, or reviewing local uncommitted changes.

Review like expert: high-confidence, evidence-first, code-aware, and willing to say "not proven" when the trail is weak. The goal is not a generic summary. The goal is to understand the bug class, find the real cause if possible, decide the best fix after reading enough code, and call out whether a larger refactor would improve the design.

## Start

Assume the current repo/worktree is on a branch that represents a PR into the mainline branch unless the user states another source/target.

Identify the review target first:

```bash
git status --short --branch
git branch --show-current
git remote show origin
git symbolic-ref --quiet refs/remotes/origin/HEAD
git log --oneline --decorate -20
```

Find the mainline branch from the user's instruction, `origin/HEAD`, repo docs, or common names such as `origin/main`, `origin/master`, `origin/trunk`, or `origin/develop`. Do not switch branches unless the user asked.

For committed branch review, inspect:

```bash
git fetch origin
git merge-base HEAD <mainline>
git diff --stat <merge-base>..HEAD
git diff --name-status <merge-base>..HEAD
git diff --color=never <merge-base>..HEAD
git log --oneline --decorate <merge-base>..HEAD
```

For uncommitted review when the user asks to review local/uncommitted changes, inspect:

```bash
git diff --stat
git diff --name-status
git diff --color=never
git diff --cached --stat
git diff --cached --name-status
git diff --cached --color=never
```

If both committed branch changes and uncommitted changes exist, separate them clearly. Treat untracked files as part of the review only after listing them with `git status --short` and reading the relevant files.

For issue trackers, hosted PRs, or review comments, use the available local/CLI/API tool for that host only when the user provides a ref or asks for it. Prefer repo-local evidence and source over web UI summaries.

For repo-local review, also inspect:

```bash
rg "<key symbol/error/config/endpoint>"
```

If the repo has local instructions, issue/PR skills, docs lists, test guidance, or maintainer runbooks, read those before deciding.

## Review Contract

Always answer these, explicitly:

- Ref/range: branch, commit range, worktree state, or uncommitted/staged scope and affected surface.
- What is the bug or behavior being fixed?
- Can we identify the root cause? If yes, where in code and why. If no, what evidence is missing.
- For regressions, who/what introduced it and when? Include commit/PR provenance when traceable by bounded history; say unknown instead of guessing.
- Is the current/proposed fix the best possible fix after reading adjacent code?
- Would a bigger refactor improve correctness, clarity, or future maintainability?
- What proof exists: tests, live repro, CI checks, docs, dependency docs/source, shipped/current behavior.
- What remains risky or unverified.

## Code Reading Depth

Read past the first touched file. Follow the real call path:

- entrypoint -> validation/parsing -> routing/dispatch -> owner module -> shared helper -> persistence/network/runtime boundary
- config/schema/docs -> runtime usage -> doctor/migration/fix path
- provider/channel/plugin owner code -> generic core seam, only if multiple owners need it
- tests around the touched surface plus adjacent regression tests

When behavior depends on a dependency, read the upstream docs/source/types or current package contract before assuming.

Prefer current source and executable proof over issue comments. Treat stale comments, old CI, and old release behavior as hints until rechecked.

## Provenance

For bug/regression reviews, include a compact `Provenance:` answer when feasible:

- Use `git log -S/-G`, `git blame`, linked PRs/issues, and tests.
- Separate author, committer/merger, and current branch/worktree author when they differ.
- Phrase as `introduced by`, `made visible by`, or `carried forward by`.
- Include confidence: `clear`, `likely`, or `unknown`.
- For features, docs, refactors, or untraceable bugs, write `N/A` or say what evidence is missing.

## Fix Quality Bar

Good fixes usually:

- live at the ownership boundary where the bug belongs
- preserve public/backward-compatible behavior unless the issue is about retiring it
- add a regression test at the smallest meaningful seam
- avoid broad special cases, hidden migrations, semantic sentinels, and provider/channel IDs in generic core
- update docs/changelog when user-visible behavior changes
- fail clearly in runtime paths and repair through doctor/migration paths when that is the established contract

Call out when a fix is only symptom-level. If a slightly larger refactor makes the invariant obvious and reduces future bugs, recommend it. If the refactor widens risk without improving the bug class, say so.

## PR Review Shape

Lead with findings when reviewing a PR/branch/worktree. Findings need file/line/symbol references and a concrete failure mode. Avoid vague "consider" comments.

If no blocking issues:

- say no blocking correctness issues found
- list the strongest proof checked
- name residual risk/test gaps
- answer whether the design is the best available shape

Do not approve, comment, close, merge, push, or land unless the user asked for that action.

## Issue Review Shape

For bugs/issues:

1. Reconstruct the reporter's scenario and affected version/surface.
2. Check whether current `master/main` already fixes it.
3. Reproduce or create a minimal local/live proof when feasible.
4. If clear, identify root cause and proposed fix.
5. If solved on `master/main`, only comment/close when the user asks; include proof and the canonical commit/PR if known.

If reproduction is not feasible, say exactly what blocks it and what evidence would make the decision reliable.

## Output Template

Use this shape when the user asks "what is this about", "is this the best fix", or "what did we fix":

```text
Ref: <branch/range/worktree/uncommitted scope>
Surface: <runtime/CLI/provider/channel/docs>

Bug: <one or two sentences>
Cause: <code path + confidence>
Provenance: <introduced/made visible/carried forward by commit/PR/date, or N/A/unknown>
Best fix: <what should change and why>
Refactor: <yes/no, specific shape>
Proof: <tests/live/CI/source/dependency docs>
Risk: <remaining uncertainty>
```

Keep it concise, but do not skip the cause/fix/refactor/proof decision.
