---
mode: agent
name: jira-integration
description: Interact with Jira Server REST API to query issues, create tickets, and transition work items for a Kanban project (no sprints). Use when the user asks to create a Jira issue, look up a ticket, search Jira, or move an issue through the workflow.
---

# GitHub Copilot Skill: jira-integration

You are GitHub Copilot. Use this skill when: Interact with Jira Server REST API to query issues, create tickets, and transition work items for a Kanban project (no sprints). Use when the user asks to create a Jira issue, look up a ticket, search Jira, or move an issue through the workflow.

## Copilot Operating Rules

- Follow user intent and repository conventions.
- Ask brief clarifying questions only when required.
- Prefer minimal, high-confidence changes.
- Validate outputs and report assumptions/limits.
- Keep responses concise and action-oriented.

## Skill Guidance

Interact with Jira Server at `https://jira.example.com` using direct REST API calls.

## Prerequisites

Each developer must set a `JIRA_TOKEN` environment variable containing their Jira Personal Access Token.

**Windows (PowerShell — persistent)**:

```powershell
[System.Environment]::SetEnvironmentVariable("JIRA_TOKEN", "<your-token>", "User")
```

After setting, restart the terminal. Verify it is set (without echoing the value):

```powershell
if ($env:JIRA_TOKEN) { "JIRA_TOKEN is set" } else { "JIRA_TOKEN is NOT set" }
```

## Security

- Do not echo the token, log `Authorization` headers, or paste tokens into Jira descriptions, commits, or chat logs.
- Treat `JIRA_TOKEN` like a password.

## Shell note

Examples below use **PowerShell** and `$env:JIRA_TOKEN`. On **bash/sh**, use `$JIRA_TOKEN` (after `export JIRA_TOKEN=...`) in place of `$env:JIRA_TOKEN`.

## Authentication

All requests use Bearer token auth:

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" "https://jira.example.com/rest/api/2/..."
```

Use `curl.exe` on Windows if `curl` is aliased to `Invoke-WebRequest`.

## API errors

- On non-success HTTP status, read the response body: Jira returns `errorMessages` and/or `errors` — surface those to the user.
- **401 Unauthorized** usually means a missing, wrong, or expired token; verify `JIRA_TOKEN` and that the shell was restarted after setting it.

## Common Queries

### Get Board ID

URL-encode query parameters (e.g. board name with spaces: `ABC%20-%20Kanban`).

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" `
  "https://jira.example.com/rest/agile/1.0/board?name=ABC%20-%20Kanban"
```

Returns Board ID `72309` (ABC - Kanban).

### Get Epics

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" `
  "https://jira.example.com/rest/agile/1.0/board/72309/epic?maxResults=50"
```

### Get Issue Details

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" `
  "https://jira.example.com/rest/api/2/issue/ABC-233"
```

### Search with JQL

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" `
  "https://jira.example.com/rest/api/2/search?jql=<URL_ENCODED_JQL>&maxResults=50"
```

Always URL-encode JQL queries. Use `maxResults` to limit responses (default 50, max 1000) and `startAt` for pagination.

## Creating Issues

### Workflow

NOTE: The target project uses a Kanban board and there are **no sprints** — do not run sprint/board sprint APIs for this project.

1. **Step 1 — Epics:** Fetch epics; display names; user picks an epic key.
2. **Step 2 — Confirm:** Confirm issue type (**Bug** or **Task**) with the user; confirm summary/description (derive one from the other if only one is given).
3. **Step 3 — Create:** POST the issue; on success parse the JSON response for **`key`** (and optionally `id`) — you need `key` for transitions.
4. **Step 4 — Transition:** List available transitions for the new issue, confirm **Selected for Development** (see below), then POST the transition. If the preferred transition is not available, show the user the allowed transitions and use an allowed one or stop with an explanation.

### Step 1: Get Epics

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" `
  "https://jira.example.com/rest/agile/1.0/board/72309/epic?maxResults=50"
```

Display epic names and let the user select. Extract the `key` (e.g. `ABC-121`).

### Step 2: Confirm type and content

- Issue type: **Bug** (`1`) or **Task** (`3`).
- Epic: the key from Step 1.
- Summary and/or description: only one may be provided — derive the other intelligently.

### Step 3: Create Issue

**Default values (auto-filled)**:

| Field    | Value                   |
| -------- | ----------------------- |
| Project  | ABC (id: `50500`)       |
| Priority | Undecided (id: `10101`) |
| Assignee | Unassigned              |

**User-provided**:

| Field       | Source                                                          |
| ----------- | --------------------------------------------------------------- |
| Summary     | User provides                                                   |
| Description | User provides (derive from summary if not given, or vice versa) |
| Issue Type  | Confirm with user: Bug (`1`) or Task (`3`)                      |
| Epic Link   | User selects in Step 1; confirmed in Step 2                     |

Only one of summary/description is needed from the user — derive the other intelligently.

```powershell
curl.exe -s -X POST `
  -H "Authorization: Bearer $env:JIRA_TOKEN" `
  -H "Content-Type: application/json" `
  -d '{
    "fields": {
      "project": {"key": "ABC"},
      "summary": "<SUMMARY_TEXT>",
      "description": "<DESCRIPTION_TEXT>",
      "issuetype": {"id": "<1_FOR_BUG_OR_3_FOR_TASK>"},
      "priority": {"id": "10101"},
      "customfield_10106": "<EPIC_KEY>"
    }
  }' `
  "https://jira.example.com/rest/api/2/issue"
```

After a successful **201** response, read **`key`** from the JSON (e.g. `ABC-456`) before calling transitions.

### Step 4: Transition to "Selected for Development"

Workflow configuration can change transition IDs. **Always** discover transitions for the new issue before POSTing:

**List available transitions:**

```powershell
curl.exe -s -H "Authorization: Bearer $env:JIRA_TOKEN" `
  "https://jira.example.com/rest/api/2/issue/<ISSUE_KEY>/transitions"
```

Find the transition whose `name` is **Selected for Development** and use its `id`. Documented default: transition ID **`61`** (status ID: **`11771`**). If that name/id is missing, use another allowed transition and tell the user, or ask how to proceed.

**Apply transition:**

```powershell
curl.exe -s -X POST `
  -H "Authorization: Bearer $env:JIRA_TOKEN" `
  -H "Content-Type: application/json" `
  -d '{"transition": {"id": "<TRANSITION_ID_FROM_LIST>"}}' `
  "https://jira.example.com/rest/api/2/issue/<ISSUE_KEY>/transitions"
```

If this POST fails, re-list transitions from the current status, show the user the valid options, and retry with a permitted `id` or stop with Jira’s error messages.

## Custom Fields

| Field     | Custom Field ID     | Value Type                           |
| --------- | ------------------- | ------------------------------------ |
| Epic Link | `customfield_10106` | String (epic key, e.g. `ABC-121`) |

## Issue Types

| Type     | ID  | Use For          |
| -------- | --- | ---------------- |
| Bug      | 1   | Problems/defects |
| Task     | 3   | Work items       |
| Story    | 8   | User stories     |
| Epic     | 7   | Large features   |
| Sub-task | 5   | Sub-items        |

## Formatting Rules

- Work only with tickets in the configured project key by default (example: `ABC`)
- All ticket references must be clickable markdown links: `[ABC-XXX](https://jira.example.com/browse/ABC-XXX)`
- Always URL-encode JQL queries and other query parameters (e.g. board name with spaces)
- Use `maxResults` to limit responses (default 50, max 1000)
- Use `startAt` for pagination
