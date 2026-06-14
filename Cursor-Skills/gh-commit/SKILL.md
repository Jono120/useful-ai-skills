---
name: gh-commit
description: >-
  Create basic git commits with repository conventions: issue-first
  Conventional Commits headers, mandatory scopes, and focused atomic changes.
  Use when the user asks to write a commit message, create a commit, or run
  git commit for repositories using the same COMMIT_CONVENTIONS.
---

# Git Commit

Create clean, focused commits that follow repository commit conventions.

## When to Apply

- User asks to create a commit
- User asks for a commit message
- User asks to run `git commit`
- User wants staged changes summarized into a valid commit header
- User wants a draft commit written

## Workflow

### 1. Gather context

Run in parallel:

```bash
git status
git diff
git log --oneline -10
```

If there are staged changes, also inspect staged diff:

```bash
git diff --staged
```

Identify:

- Issue reference (for example `ABC-482`) or `noissue` for docs/housekeeping-only work
- Valid commit `type` and mandatory `scope`
- Whether changes are focused enough for one commit

### 2. Validate commit header format

Required:

```
<issue-ref>: <type>(<scope>): <description>
```

No issue:

```
noissue: <type>(<scope>): <description>
```

Allowed `type` values:

- `build`
- `chore`
- `ci`
- `docs`
- `feat`
- `fix`
- `perf`
- `refactor`
- `revert`
- `style`
- `test`

Allowed `scope` values (mandatory):

- `repo`
- `core`
- `api`
- `ui`
- `infra`
- `docs`
- `support`

Description rules:

- Imperative mood
- Specific and concise
- Describes intent, not just file names

Examples:

- `ABC-482: feat(api): add filtering endpoint`
- `OPS-517: fix(infra): harden OpenTelemetry exporter config`
- `noissue: docs(repo): clarify branching strategy`

### 3. Stage intended files only

Stage only relevant changes for one logical unit of work:

```bash
git add <paths>
```

Avoid mixing unrelated files. If changes are mixed, propose splitting into multiple commits.

### 4. Commit using HEREDOC

Create the commit with a HEREDOC message:

```bash
git commit -m "$(cat <<'EOF'
<issue-ref>: <type>(<scope>): <description>

<optional body: why/context, 1-2 short lines>
EOF
)"
```

Body guidance:

- Optional but recommended for non-trivial changes
- Explain why, constraints, or follow-up context
- Keep concise

### 5. Verify result

Run:

```bash
git status
git log -1 --pretty=full
```

Confirm the new commit header is valid and the working tree is in the expected state.

## Safety Rules

- Never commit secrets (`.env`, credentials, private keys)
- Never commit unrelated changes just to clean the tree
- Do not amend unless the user explicitly asks, or hooks modified files after a successful commit
- If hooks fail, fix issues and create a new commit

## Pre-commit Validation Checklist

- [ ] Commit header matches required format
- [ ] Type is from allowed list
- [ ] Scope is from allowed list and present
- [ ] Issue reference is valid, or `noissue` is justified
- [ ] Staged diff contains one logical change
- [ ] No secrets or generated noise accidentally staged

## Output Mode

If the user asks for a draft only, provide:

- `Suggested commit header`
- `Optional commit body`
- `Suggested git add commands`

If the user asks to execute, perform stage + commit + verification and report the resulting commit hash.

