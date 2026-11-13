# Review scope, passes and findings

## Scope

Default is uncommitted; the other scopes only when the user asks for them. Done when you hold one diff command and the complete list of untracked files.

- Uncommitted (default): `git --no-pager diff --color=never HEAD -- <paths>` (staged and unstaged), and the untracked files by path.
- Branch (everything the branch would merge into master/main): `git fetch origin`, then `git --no-pager diff --color=never $(git merge-base origin/<mainline> HEAD)`. It diffs the working tree, so commits and uncommitted work land in one diff; add the untracked files and `git log --oneline <merge-base>..HEAD` for the commit story. Mainline from `origin/HEAD`.
- Hosted PR: check it out (or use its worktree) and review it as a branch, with the PR's target branch as mainline; the host's CLI (`gh pr view`, `az repos pr show`, `tea pulls`) gives the target, description and existing comments.

## Review passes

First read the repo's `AGENTS.md` / README, then the full touched files and their callers, until you can state what the change does and why. Run every pass over the whole scope; a pass is done when it has findings or an explicit "nothing found". Settle uncertain semantics with cheap real experiments: a local `python -m http.server` plus real `curl`, a scratch script, a focused test run.

1. **Bugs** — wrong behaviour on any path: edge cases, error handling, concurrency, security, compatibility.
2. **Simplify** — the same behaviour with less: dead or duplicated code, needless indirection, seams only tests use, logic an existing helper, library or framework feature already covers, branches that collapse, a simpler data shape. Plus **slop**: narrating comments and logging, defensive checks and `try`/`catch` on trusted paths, casts that only silence the type checker, nesting an early return flattens, naming and error handling that drift from the surrounding code.
3. **Optimize** — the same behaviour, cheaper: redundant I/O, queries or network calls, N+1 patterns, repeated work in loops, avoidable allocations, blocking calls. Each finding names the cost it saves and whether that path is hot enough to matter.
4. **Tests** — each test guards a real behaviour or regression. Check every touched test against the Junk patterns in [`test-audit`](../test-audit/SKILL.md); a match is a finding unless that skill's Retention bar names the contract it guards.

## Findings

Open with a one-line **summary** of what the change does. Then the findings, numbered F1, F2, … and ranked by severity within each pass; each carries `file:line`, the failure or cost it addresses, a concrete diff, a verdict **worth doing** or **nitpick**, and **unverified** when not proven. Close with the overall **verdict**, one line:

- `Ship` — no worth-doing findings.
- `Ship after fixes: F1, F3` — the approach holds; the listed findings come first.
- `Rethink: <reason>` — the approach itself is wrong.
