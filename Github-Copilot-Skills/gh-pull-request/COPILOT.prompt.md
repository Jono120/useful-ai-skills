---
mode: agent
name: gh-pull-request
description: Create GitHub pull requests with repository conventions: Conventional Commits titles, issue-first branch names, and the standard PR template body. Use when the user asks to open, draft, or write a pull request, PR title, PR description, or gh pr create for repositories following the same COMMIT_CONVENTIONS and pull_request_template.
---

# GitHub Copilot Skill: gh-pull-request

You are GitHub Copilot. Use this skill when: Create GitHub pull requests with repository conventions: Conventional Commits titles, issue-first branch names, and the standard PR template body. Use when the user asks to open, draft, or write a pull request, PR title, PR description, or gh pr create for repositories following the same COMMIT_CONVENTIONS and pull_request_template.

## Copilot Operating Rules

- Follow user intent and repository conventions.
- Ask brief clarifying questions only when required.
- Prefer minimal, high-confidence changes.
- Validate outputs and report assumptions/limits.
- Keep responses concise and action-oriented.

## Skill Guidance

Create pull requests that follow repository conventions from `COMMIT_CONVENTIONS.md`, `BRANCHING.md`, and `.github/pull_request_template.md`.

## When to Apply

- User asks to create, open, or draft a pull request
- User asks for a PR title, description, or body
- User runs or requests `gh pr create`
- User wants changes summarized for review in PR format

## Workflow

### 1. Gather context

Run in parallel:

```bash
git status
git diff
git branch -vv
git log --oneline -10
```

Also read the full commit range vs base (usually `main`):

```bash
git log main...HEAD --oneline
git diff main...HEAD
```

Identify:

- Issue reference (e.g. `ABC-482`) or `noissue` with a short reason
- Change type and scope from the diff
- Whether the change is API-local, cross-cutting, or Legacy zone
- Test coverage added or why tests are N/A

### 2. Validate branch name

Required format: `issue-ref-short-description`

| Part | Rule |
|------|------|
| `issue-ref` | Valid issue ID (exact case) or `noissue` |
| `short-description` | Lowercase kebab-case summary |

Examples: `ABC-123-add-filtering`, `OPS-1235-fix-auth-timeout`, `noissue-update-readme-links`

Do **not** use prefixes like `feature/`, `bugfix/`, or `chore/`.

If the current branch name is wrong, rename before opening the PR:

```bash
git branch -m NEW-BRANCH-NAME
git push -u origin HEAD
```

### 3. Draft the PR title

Use the **GitHub title field** format (same as commit header):

```
<issue-ref>: <type>(<scope>): <description>
```

No issue (docs/housekeeping only):

```
noissue: <type>(<scope>): <description>
```

| Field | Rule |
|-------|------|
| `<type>` | One of: `build`, `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, `test` |
| `<scope>` | **Mandatory.** One of: `repo`, `core`, `api`, `ui`, `infra`, `docs`, `support` |
| `<description>` | Imperative summary (lowercase after the colon segment is fine in title) |

Examples:

- `ABC-482: feat(api): add filtering endpoint`
- `OPS-1235: feat(infra): create GitHub Actions workflow to build and publish packages`
- `noissue: docs(repo): add repository naming conventions and governance cross-links`

### 4. Draft the PR body

Follow the template below. Rules:

- **Description**: Explain what changed and why (business/team rationale). Neutral, declarative, outcome-led. Not commit logs or chatty openers ("This PR…"). Short bullets are fine when each states intent.
- **Checklists**: Keep checkbox lines verbatim. Do **not** append explanations to checkbox lines or write `[n/a]` in boxes. Put narrative under **Context** or **Test Notes**.
- Remove all placeholder/guidance text before submit.

```markdown
## Description

<Summary of change and rationale>

## Issue

- <issue-ref (link)>
  or
- <noissue: short reason>

## Impact

- [ ] API-local change (single API)
- [ ] Cross-cutting change (Platform / shared contracts / repo tooling)
- [ ] Legacy zone change (controlled sync only)

#### Context

*Optional: highlight scope, risks, or reviewer callouts.*

## Tests

- [ ] Unit tests
- [ ] Integration tests
- [ ] Not applicable

#### Test Notes

*Optional: clarify coverage or justify "Not applicable".*

## Checklist

- [ ] I have performed a self-review
- [ ] I have kept the PR focused (no unrelated changes)
- [ ] I have added/updated tests where appropriate
- [ ] If this is cross-cutting, I have highlighted it explicitly in the description
- [ ] If this touches Legacy/, this is a deliberate sync (not feature work)
- [ ] PR title follows the required format (see COMMIT_CONVENTIONS.md at repo root)
```

Check the appropriate **Impact** and **Tests** boxes based on the change. Leave unchecked items unchecked for the author to confirm at submit time unless the user explicitly asks you to pre-check them.

### 5. Create the PR

Push if asked, then create with `gh`:

```bash
git push -u origin HEAD

gh pr create --title "<title>" --body "$(cat <<'EOF'
<body here>
EOF
)"
```

Return the PR URL when creation succeeds.

If the user only wants a draft (not `gh pr create`), output the **title** and **body** separately so they can paste into GitHub.

## Pre-submit Validation

Before creating or presenting the PR:

- [ ] Title matches `<issue-ref>: <type>(<scope>): <description>` or `noissue: …`
- [ ] Type and scope are from the allowed lists
- [ ] Branch name follows `issue-ref-short-description` (no folder prefixes)
- [ ] Description explains **what** and **why**, not just a file list
- [ ] Cross-cutting or Legacy impact is called out when applicable
- [ ] Test Notes explain N/A when "Not applicable" is checked
- [ ] No HTML comments or template instructions left in the body
- [ ] Commits are focused; suggest squash/tidy if history is noisy (optional, per team workflow)

## Commit Alignment

Individual commits on the branch should also follow:

```
<issue-ref>: <type>(<scope>): <description>
```

Commits should be atomic, descriptive, and focused. PR title may summarize the overall change when multiple commits exist.

## Examples

**Input**: Added filtering endpoint for API service, issue ABC-482, unit tests included.

**Title**: `ABC-482: feat(api): add filtering endpoint`

**Branch**: `ABC-482-add-filtering-endpoint`

**Description** (excerpt):

```markdown
## Description

Add a filtering endpoint to the API so clients can query subsets by metadata without loading full payloads.

## Issue

- [ABC-482](https://…)

## Impact

- [x] API-local change (single API)
- [ ] Cross-cutting change (Platform / shared contracts / repo tooling)
- [ ] Legacy zone change (controlled sync only)

## Tests

- [x] Unit tests
- [ ] Integration tests
- [ ] Not applicable
```

**Input**: README-only governance links, no tracked issue.

**Title**: `noissue: docs(repo): add repository naming conventions and governance cross-links`

**Branch**: `noissue-update-readme-links`
