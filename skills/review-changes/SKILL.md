---
name: review-changes
description: Read-only review of uncommitted changes, a branch or a PR — ranked findings, you pick what to apply.
disable-model-invocation: true
---

# Review Changes

A **read-only** review: you report findings and the user picks what to apply. For simplifications and optimizations applied straight away, the user runs `simplify-optimize` instead.

Scope, review passes and findings format: [`REVIEW.md`](REVIEW.md) — read it first.

## Steps

1. **Scope** — per REVIEW.md.
2. **Passes** — all four, per REVIEW.md.
3. **Report** — the findings, then the checks you actually ran. Done when the report ends with a question: which findings to apply?

Applying, when the user picks: make those changes, rerun the repo's tests, lint, format and `git diff --check` for the touched area, and report the result.
