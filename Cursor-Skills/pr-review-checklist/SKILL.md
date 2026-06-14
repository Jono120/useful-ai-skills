---
name: pr-review-checklist
description: >-
  Review pull requests with a deterministic checklist for correctness, risk,
  tests, and maintainability. Use when the user asks for a PR review, review
  rubric, or merge-readiness check.
---

# PR Review Checklist

Apply a consistent review standard before merge.

## When to Apply

- User asks to review code changes
- User asks if a pull request is merge-ready
- User asks for structured review feedback

## Workflow

### 1. Collect review context

- PR scope and linked issue
- Changed files and architectural surface area
- Test evidence (automated/manual)

### 2. Run checklist

Use `templates/review-checklist.md` and evaluate every section.

### 3. Report findings by severity

- `Critical`: must fix before merge
- `Major`: high-value correction before merge
- `Minor`: suggestion or follow-up

### 4. Confirm recommendation

Return one:

- `Approve`
- `Approve with follow-ups`
- `Request changes`

## Output Mode

Return:

- `Findings` (ordered by severity)
- `Open questions/assumptions`
- `Review recommendation`

