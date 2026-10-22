# Agents

Skills I use when working with agents. Each skill is a set
of instructions in a `SKILL.md` file that a coding agent reads when you invoke it.

## Choose a skill

- [review-pr](skills/review-pr/SKILL.md) reviews a branch for bugs, security issues, performance problems, and gaps in testing. It reports findings without editing files.
- [deep-review-pr](skills/deep-review-pr/SKILL.md) follows the code beyond the diff to check the root cause, assess the fix, and identify what remains unproven. It also handles reviews of local, uncommitted changes.
- [test-audit](skills/test-audit/SKILL.md) checks whether tests catch meaningful regressions. Use it when writing tests or auditing an existing suite. It includes a guide for auditing a whole subsystem.
- [rebase-on-main](skills/rebase-on-main/SKILL.md) rebases the current branch onto the repository's mainline, works through conflicts, and validates the result. It stops before pushing.

## Other useful skills

- [security-audit](https://github.com/cloudflare/security-audit-skill) from Cloudflare audits a codebase for vulnerabilities across real trust boundaries, validates each candidate with a fresh agent, and writes a structured report.

## Install a skill

Copy the whole skill directory into your agent's skills directory. Keep its
supporting files and license together.

For example, run this from the repository root to install `test-audit` for Claude Code:

```sh
mkdir -p ~/.claude/skills
cp -R skills/test-audit ~/.claude/skills/
```

Then ask your agent to use `test-audit`. Installation paths and invocation syntax vary by agent.

## Credits

[test-audit](skills/test-audit/SKILL.md), including its campaign guide, comes from
[OpenClaw](https://github.com/openclaw/openclaw/tree/main/.agents/skills/test-audit).
I adapted it to use each repository's own tools and validation commands.

[deep-review-pr](skills/deep-review-pr/SKILL.md) is adapted from
[github-deep-review by Peter Steinberger (steipete)](https://github.com/steipete/agent-scripts/blob/main/skills/github-deep-review/SKILL.md). I added branch, worktree, and local-change review workflows and removed reliance on a specific hosting service.

`review-pr`, `rebase-on-main`, and my changes to the adapted skills are available under the root [MIT license](LICENSE).

Keep the applicable license notices when copying or redistributing these skills.
