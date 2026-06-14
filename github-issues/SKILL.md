---
name: github-issues
description: Interact with GitHub Issues to search, create, update, and manage issue state using GitHub API/CLI. Use when the user asks to open an issue, find an issue, add labels, assign owners, or close/reopen issues.
---

# GitHub Issues Integration

Interact with GitHub Issues for a repository via GitHub API or `gh`.

## Prerequisites

Use authenticated GitHub CLI (`gh auth status`) or set `GH_TOKEN`.

**Windows (PowerShell - persistent)**:

```powershell
[System.Environment]::SetEnvironmentVariable("GH_TOKEN", "<your-token>", "User")
```

Restart terminal, then verify:

```powershell
if ($env:GH_TOKEN) { "GH_TOKEN is set" } else { "GH_TOKEN is NOT set" }
```

## Security

- Never print token values or auth headers.
- Do not put secrets into issue title/body/comments.
- Treat `GH_TOKEN` as sensitive.

## Defaults

Use current repository from git remote when possible. If ambiguous, ask user to confirm:

- Owner: `<OWNER>`
- Repo: `<REPO>`

## Authentication

Prefer `gh api` for reliability:

```powershell
gh api repos/<OWNER>/<REPO>/issues
```

If not using `gh`, use REST headers:

```powershell
curl.exe -s `
  -H "Authorization: Bearer $env:GH_TOKEN" `
  -H "Accept: application/vnd.github+json" `
  "https://api.github.com/repos/<OWNER>/<REPO>/issues"
```

## API errors

- Surface non-2xx status and response message.
- `401/403`: token missing, expired, or missing scopes.
- `404`: wrong owner/repo or private repo without access.

## Common Queries

### List open issues

```powershell
gh api "repos/<OWNER>/<REPO>/issues?state=open&per_page=50"
```

### Get issue details

```powershell
gh api repos/<OWNER>/<REPO>/issues/<NUMBER>
```

### Search issues by keyword

```powershell
gh api "search/issues?q=repo:<OWNER>/<REPO>+is:issue+<QUERY>"
```

### List labels

```powershell
gh api repos/<OWNER>/<REPO>/labels
```

## Creating and Managing Issues

### Workflow

1. Confirm owner/repo and issue content (title/body).
2. Create issue with optional labels/assignees/milestone.
3. Return issue number and clickable URL.
4. Apply follow-up updates (state, labels, assignees) as requested.

### Create issue

```powershell
gh api repos/<OWNER>/<REPO>/issues `
  --method POST `
  --field title="<TITLE>" `
  --field body="<BODY>"
```

Optional fields:

- `--field assignees[]="<USERNAME>"`
- `--field labels[]="bug"`
- `--field labels[]="enhancement"`
- `--field milestone="<MILESTONE_NUMBER>"`

### Update issue content

```powershell
gh api repos/<OWNER>/<REPO>/issues/<NUMBER> `
  --method PATCH `
  --field title="<UPDATED_TITLE>" `
  --field body="<UPDATED_BODY>"
```

### Close or reopen issue

```powershell
gh api repos/<OWNER>/<REPO>/issues/<NUMBER> `
  --method PATCH `
  --field state="closed"
```

Set `state="open"` to reopen.

### Add comment

```powershell
gh api repos/<OWNER>/<REPO>/issues/<NUMBER>/comments `
  --method POST `
  --field body="<COMMENT_TEXT>"
```

## Formatting Rules

- Always return issue references as clickable markdown:
  `[#123](https://github.com/<OWNER>/<REPO>/issues/123)`.
- Use repository-scoped queries first before global search.
- Keep list operations paged (`per_page`, pagination) for large repos.
- Distinguish PRs from issues (GitHub issues endpoint can include PR objects).
