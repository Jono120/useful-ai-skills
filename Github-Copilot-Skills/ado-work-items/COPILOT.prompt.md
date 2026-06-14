---
mode: agent
name: ado-work-items
description: Interact with Azure DevOps REST API to query work items, create bugs/tasks, and transition work through Kanban states. Use when the user asks to create an ADO work item, look up a ticket, search boards, or move work item status.
---

# GitHub Copilot Skill: ado-work-items

You are GitHub Copilot. Use this skill when: Interact with Azure DevOps REST API to query work items, create bugs/tasks, and transition work through Kanban states. Use when the user asks to create an ADO work item, look up a ticket, search boards, or move work item status.

## Copilot Operating Rules

- Follow user intent and repository conventions.
- Ask brief clarifying questions only when required.
- Prefer minimal, high-confidence changes.
- Validate outputs and report assumptions/limits.
- Keep responses concise and action-oriented.

## Skill Guidance

Interact with Azure DevOps via REST API for a single organization and project.

## Prerequisites

Each developer must set `ADO_TOKEN` as a Personal Access Token with Work Items scope.

**Windows (PowerShell - persistent)**:

```powershell
[System.Environment]::SetEnvironmentVariable("ADO_TOKEN", "<your-token>", "User")
```

After setting, restart terminal and verify (without printing value):

```powershell
if ($env:ADO_TOKEN) { "ADO_TOKEN is set" } else { "ADO_TOKEN is NOT set" }
```

## Security

- Never print or log token values.
- Do not paste secrets in work item fields, commits, or chat.
- Treat `ADO_TOKEN` as a password.

## Shell note

Examples use PowerShell and `$env:ADO_TOKEN`. On bash/sh, use `$ADO_TOKEN`.

## Defaults

Use these placeholders unless the user provides different values:

- Organization: `<ORG>`
- Project: `<PROJECT>`
- Team: `<TEAM>`
- Board: `<BOARD>`

## Authentication

Azure DevOps PAT uses Basic auth with empty username:

```powershell
$pair = ":$env:ADO_TOKEN"
$b64 = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes($pair))
curl.exe -s -H "Authorization: Basic $b64" "https://dev.azure.com/<ORG>/<PROJECT>/_apis/wit/workitems/1?api-version=7.1"
```

## API errors

- For non-success responses, surface status code and response body.
- `401` usually means token missing/expired or insufficient scopes.
- `404` often means wrong org/project/team path.

## Common Queries

### Get team iteration/team settings

```powershell
curl.exe -s -H "Authorization: Basic $b64" `
  "https://dev.azure.com/<ORG>/<PROJECT>/<TEAM>/_apis/work/teamsettings?api-version=7.1"
```

### Query work items with WIQL

```powershell
curl.exe -s -X POST `
  -H "Authorization: Basic $b64" `
  -H "Content-Type: application/json" `
  -d '{
    "query": "SELECT [System.Id], [System.Title], [System.State] FROM WorkItems WHERE [System.TeamProject] = ''<PROJECT>'' ORDER BY [System.ChangedDate] DESC"
  }' `
  "https://dev.azure.com/<ORG>/<PROJECT>/_apis/wit/wiql?api-version=7.1"
```

### Get work item details

```powershell
curl.exe -s -H "Authorization: Basic $b64" `
  "https://dev.azure.com/<ORG>/<PROJECT>/_apis/wit/workitems/<ID>?api-version=7.1"
```

## Creating Work Items

### Workflow

1. Confirm work item type (**Bug** or **Task** by default).
2. Confirm title/description/acceptance criteria from user input.
3. Create the item with JSON Patch.
4. If requested, transition state by updating `System.State`.

### Create Bug

```powershell
curl.exe -s -X POST `
  -H "Authorization: Basic $b64" `
  -H "Content-Type: application/json-patch+json" `
  -d '[
    { "op": "add", "path": "/fields/System.Title", "value": "<TITLE>" },
    { "op": "add", "path": "/fields/System.Description", "value": "<DESCRIPTION>" },
    { "op": "add", "path": "/fields/System.AreaPath", "value": "<PROJECT>" },
    { "op": "add", "path": "/fields/System.IterationPath", "value": "<PROJECT>" }
  ]' `
  "https://dev.azure.com/<ORG>/<PROJECT>/_apis/wit/workitems/\$Bug?api-version=7.1"
```

### Create Task

Use the same payload but endpoint `workitems/$Task`.

### Transition state

Always fetch the current work item first, then patch state:

```powershell
curl.exe -s -X PATCH `
  -H "Authorization: Basic $b64" `
  -H "Content-Type: application/json-patch+json" `
  -d '[
    { "op": "add", "path": "/fields/System.State", "value": "<NEW_STATE>" }
  ]' `
  "https://dev.azure.com/<ORG>/<PROJECT>/_apis/wit/workitems/<ID>?api-version=7.1"
```

If state transition fails, show the error and propose valid next states from workflow rules.

## Issue Types

- `Bug`: defect tracking
- `Task`: implementation work
- `User Story`: customer-facing requirement
- `Epic`: large initiative

## Formatting Rules

- Default to one project unless user asks cross-project queries.
- Render work item references as markdown links:
  `[AB#123](https://dev.azure.com/<ORG>/<PROJECT>/_workitems/edit/123)`.
- Keep queries bounded (top/order by/date filters) to avoid large payloads.
