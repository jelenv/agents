---
name: review-pr
description: Code review checking for bugs, quality, security, performance, best practices and gaps before merging or committing it. Use when explictly asked to do a "review-pr". Do NOT use for /review action or other review tasks.
---

# PR Review Skill

Reviews the current checked-out branch against master (or main), providing actionable feedback on code quality, bugs, and improvements.

## Workflow

1. **Get the diff**: Run `git diff master...HEAD` to see all changes
2. **Identify changed files**: Run `git diff --name-only master...HEAD` to list affected files
3. **Review each file**: Read the full context of significantly changed files
4. **Provide structured feedback**

## Review Checklist

For each change, evaluate:

### Code Quality

- Is the code readable and well-structured?
- Are there any code smells or anti-patterns?
- Does it follow the project's conventions?

### Correctness

- Are there any bugs or logic errors?
- Are edge cases handled?
- Are error conditions handled properly?

### Security

- Are there any security vulnerabilities?
- Is user input validated?
- Are secrets/credentials exposed?

### Testing

- Are changes covered by tests?
- Are the tests meaningful?

### Performance

- Are there any obvious performance issues?
- Any unnecessary allocations, N+1 queries, or blocking calls?

## Output Format

Be terse in your review comments but precise.

Structure your review as:

```
## Summary
Brief overview of the changes and their purpose.

## Files Changed
List of files with brief description of changes.

## Issues Found
### 🔴 Critical
Must fix before merge.

### 🟡 Suggestions
Recommended improvements.

### 🟢 Nitpicks
Minor style/preference notes.

## Verdict
APPROVE / REQUEST CHANGES / NEEDS DISCUSSION
```

## Tips

- Use `git log master..HEAD --oneline` to understand commit history
- For large diffs, use `git diff master...HEAD --stat` first to prioritize
- Read related code for context, not just the diff
- Consider the "why" behind changes, not just the "what"
- If you are not sure about your conclusions, verify or ask for more information
- Don't edit any files, just read and review the changes
- You are welcome to propose improvements where appropriate
- For a hosted PR, use the available CLI or API for that host to read its description, diff, and review threads. Post comments or votes only when the user explicitly asks.
