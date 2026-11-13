---
name: simplify-optimize
description: Find and apply behaviour-preserving simplifications, slop cleanup and optimizations in uncommitted changes, a branch or a PR.
disable-model-invocation: true
---

# Simplify & Optimize

A **refactor**: the same behaviour with less code or less cost, applied straight away. Scope and the Simplify and Optimize passes come from [`REVIEW.md`](../review-changes/REVIEW.md) — read it first. Bugs you notice on the way go into the report as findings, left unfixed.

## Steps

1. **Scope** — per REVIEW.md.
2. **Baseline** — run the repo's tests for the touched area. Done when they are green, or the user has acknowledged the red ones you report.
3. **Passes** — Simplify and Optimize, per REVIEW.md.
4. **Apply** — every **worth doing** finding gets applied; each **nitpick** is a judgement call: apply the cheap, clearly better ones, skip the rest with a one-line reason. Prefer net-negative LOC. Done when no finding is left without one of the two.
5. **Recheck** — rerun the same tests, plus lint, format and `git diff --check` for the touched files. Done when they match the baseline.
6. **Report** — what changed (with the cost saved for each optimization), what was skipped and why, bugs noticed, checks actually run. Done when the report is sent with a question: commit?
