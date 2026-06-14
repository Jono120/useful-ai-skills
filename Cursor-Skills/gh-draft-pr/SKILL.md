---
name: create-draft-pr
description: Create a draft pull request on GitHub by gathering branch context, filling in the repository PR template, pushing the branch, and creating the PR via GitHub MCP (not the gh CLI). Use when the user asks to create a PR, push and create a PR, make a PR, or submit a draft PR.
---

# Create Draft PR

Create a properly formatted draft PR by gathering branch context and filling the repository's PR template.

**Governance** (source of truth):

- [`CHARTER.md`](../../../CHARTER.md) — repository zones (`Apis/` vs `Platform/` vs `Legacy/` vs repo-wide)
- [`COMMIT_CONVENTIONS.md`](../../../COMMIT_CONVENTIONS.md) — commit and PR title format, allowed types/scopes
- [`BRANCHING.md`](../../../BRANCHING.md) — branch naming
- [`.github/pull_request_template.md`](../../../.github/pull_request_template.md) — PR body structure

## Instructions

### Step 1: Gather branch context

**Base branch**

- Default to `main`.
- If the branch name or context implies a different base (e.g. work stacked on another feature branch), confirm with the user.

**Git commands** — run each as a **separate Shell tool call** (independent commands may run in parallel):

1. `git rev-parse --abbrev-ref HEAD` (branch name)
2. `git log <base>..HEAD --oneline --no-decorate`
3. `git diff <base>...HEAD --stat`

**Why two different ranges**

- `git log <base>..HEAD` — commits on your branch that are not ancestors of `<base>` (good for message history on this branch).
- `git diff <base>...HEAD` — **three-dot** diff: changes from the **merge base** of `<base>` and `HEAD` through `HEAD` (what the PR branch adds versus the target tip when histories have diverged).

**Parse the branch name for metadata**

- **Issue ref** (from the **branch name** only)
  - Shape: `issue-ref-short-description` with kebab-case `short-description`.
  - **No** grouping prefixes (`feature/`, `fix/`, `chore/`, etc.) — see [`BRANCHING.md`](../../../BRANCHING.md).
  - Treat the leading token as the ref: `ABC-###`, `OPS-####`, other tracker keys (`PROJECT-###`, `AB#####`, ...), or `noissue`.
  - Preserve **exact tracker casing**; do not assume a single project prefix.
  - If you cannot parse the ref or split ref vs. description, ask the user.

- **Type** (for the PR title)
  - Branch names **do not** encode type.
  - Prefer **commit messages** on `<base>..HEAD` in conventional form: `type(scope):` or `issue-ref: type(scope):` (latest commit primary; if the branch has a single commit, use that).
  - If messages are non-conventional or ambiguous, infer from the nature of the change.
  - Must be one of **Allowed `type` values** below (`COMMIT_CONVENTIONS.md`).

- **Scope** (for the PR title)
  - Branch names **do not** encode scope.
  - Prefer `scope` from commit messages when valid.
  - Otherwise infer from **changed paths** using **Allowed `scope` values** below.

**Allowed `type` values** (must be one of; see `COMMIT_CONVENTIONS.md`):

`build`, `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, `test`

**Allowed `scope` values** (must be one of):

| Scope               | Typical paths / signals |
| ------------------- | ----------------------- |
| `api`               | `src/api/`, `services/api/` |
| `ui`                | `src/ui/`, `web/` |
| `core`              | `src/core/`, shared business logic |
| `infra`             | `infra/`, `deploy/`, CI/CD files |
| `docs`              | `docs/`, `README.md` |
| `support`           | `support/`, operational tooling |
| `repo`              | Root-level tooling, `.github/`, `.cursor/`, build files, or cross-area work where one scope would mislead — confirm with the user if unsure |

**When several scopes fit**

- Pick the **dominant** scope by change size.
- Evenly mixed API + `Platform/`: prefer `platform` or ask the user.

#### Enrich with Jira ticket (if available)

- If the issue ref matches a supported Jira key for this workflow (for example `ABC-####` or `OPS-####`), fetch summary and description via the Jira REST API **when** `.cursor/skills/jira-integration/SKILL.md` is available (base URL, auth, examples there).
- Use the ticket’s problem statement and acceptance criteria as the main input for **business context** in the PR description.

#### Enrich with agent transcripts (if available)

Use a **subagent** (Task tool) so large transcript files do not fill the main context.

**Where transcripts live** — They are **not** in the git repository. Cursor stores parent-chat transcripts under the user’s Cursor application data, in an `agent-transcripts` directory for this workspace’s Cursor project.

**Resolving the path** — Prefer a concrete path when the **Cursor workspace / agent context** supplies it (e.g. system or workspace rules that name the `agent-transcripts` folder for this project). Otherwise infer the usual layout on the machine running Cursor (often a `.cursor/projects/.../agent-transcripts`-style path under the user’s Cursor data). If you cannot resolve it, **skip** transcript enrichment or ask the user for the folder path.

The subagent should:

1. List files in that resolved `agent-transcripts` directory, newest first (by modification time).
2. Read the 3–5 most recent transcripts.
3. Match to this branch: compare files discussed in transcripts to paths from the diff stat (pass the diff stat in the prompt).
4. Return at most 3–5 sentences: motivation, key decisions, problems solved.

Use that summary when Jira and commits alone do not explain **why** the change exists.

### Step 2: Read PR template

Read `.github/pull_request_template.md` from the repository root.

### Step 3: Build PR title

**Format** (must match `COMMIT_CONVENTIONS.md` and the PR template):

`<issue-ref>: <type>(<scope>): <description>`

**Fields** (from Step 1)

- `<issue-ref>` — branch or user (e.g. `ABC-233`, `OPS-1234`, `noissue`)
- `<type>` — must be one of **Allowed `type` values** (Step 1; `COMMIT_CONVENTIONS.md`)
- `<scope>` — exactly one **Allowed `scope` value** (Step 1); in the title use **parentheses** around the scope token, not square brackets
- `<description>` — imperative and concise (from commits + diff, not raw file names)

**Examples**

- `ABC-233: docs(repo): harden Jira skill`
- `ABC-482: feat(api): add filtering endpoint`
- `noissue: chore(repo): update pull request template`

### Step 4: Fill the PR body

Fill the template sections (see `.github/pull_request_template.md`).

**Map template sections to sources**

- **PR title** — Step 3 string goes **only** in GitHub’s **title** field. Detailed title rules live in the template’s top HTML block (remove that block before submit). The Markdown **body** starts at **Description**.

- **Description** — From commits and diff (see **Description guidelines** below).

- **Issue** — `[issue-ref](issue-url)` or `noissue` + short reason; patterns under **Issue link patterns** below.

- **Impact** — Pre-check checkboxes only when path rules below apply; user may adjust. Narrative belongs in **#### Context**, not on checkbox lines.

- **Tests** — Follow **Tests** auto-check rules below. `Not applicable` is mutually exclusive with `Unit tests` and `Integration tests`; those two may both be pre-checked when the diff matches both. Put commands, links, and N/A rationale in **#### Test Notes**, not on checkbox lines.

- **Checklist** — Auto-check only what the skill can verify (rules below). Keep checklist lines **verbatim**.

#### Description guidelines

- **Length** — about **500 characters** max for the main description.
- **Focus** — *why* the change exists and who it helps; **intent and behavior**, aligned with the template (not a commit list or a file list that duplicates the diff).
- **Avoid** — implementation trivia reviewers can see in the diff; **diff narration** disguised as Description bullets.
- **Formatting** — no horizontal rules (`---`); section headings are enough.

**Tone and structure**

- Calm, **neutral**, and **declarative**. Write for reviewers **scanning** the description, not for narrative or chatty setup.
- Prefer imperative **outcomes** (`Add … to enable …`, `Align … with …`, `Clarify …`, `Refactor … to improve …`).
- Avoid “This PR introduces…”, “In this change…”, first person (“I added…”, “We decided to…”), and similar filler.
- A few short sentences or a short list reads better than one long sentence.
- One idea per sentence or bullet. Skip long parentheticals and comma-lists of unrelated points.
- Bullets belong to **intent** and **outcomes**, not to replaying the diff or listing paths.
- Avoid hype. Bold **sparingly** (for example the main deliverable on first mention).
- Open with what the PR **does** or **adds**. Do not use a “Primary change:” label if it only repeats that line.
- Match the structure to the change:
  - Single change: one concise sentence.
  - Main change + minor adjustments: lead with the main outcome, then short bullets.
  - Multiple changes: use bullets; do not force a grouping label.
- Use **Supporting changes** only when it improves clarity; do not use it by default.
- Do not produce file-based bullet lists; reference files only when necessary.
- **Filenames** — optional. Use when they disambiguate, carry domain meaning, or pin a very local edit. Otherwise use plain language and no path-first bullets.

**Before submit / ready for review** — apply **TEMPLATE GUIDANCE** from `.github/pull_request_template.md`:

- Remove the entire top HTML instruction block.
- Remove remaining HTML comments in the body (e.g. under Tests).
- Replace `<required-input>` placeholders with real content.
- Replace or drop optional *italic* prompts as appropriate.

#### Checklist auto-check rules

- Auto-check **only** what the skill can verify.
- Keep every checklist line **verbatim** in the PR body.

**Impact** — Classification inputs:

- Zones and intent: [`CHARTER.md`](../../../CHARTER.md).
- **API roots** — each `Apis/<Primitive|Business|Application>/<ApiFolder>/` is one API (align with **Allowed `scope` values** paths; current folders include `PointClouds`, `TerrestrialScans`, `Trcps`, `Support`).
- **Evidence** — changed paths from `git diff <base>...HEAD --stat` (or equivalent file list).

- [x] `API-local change (single API)`
  - **Pre-check** when **every** changed path sits under **exactly one** API root subtree.
  - **Do not** pre-check if **any** path is outside that subtree, including:
    - `Platform/`, `Legacy/`, a second API root
    - Repo-level paths: `Docs/`, `.github/`, `.cursor/`, `Directory.*.props`, root solution/build files, `global.json`, `.justfile`
  - If ambiguous, leave unchecked and flag for the user.

- [x] `Cross-cutting change (Platform / shared contracts / repo tooling)`
  - **Pre-check** when **any** of the following is true:
    - Path under `Platform/` (shared contracts, observability, messaging, middleware — [`CHARTER.md`](../../../CHARTER.md))
    - Path under repo tooling / governance (same idea as scope `repo`: `Docs/`, `.github/`, `.cursor/`, `Directory.*.props`, solution/build at repo root, `global.json`, `.justfile`, …)
    - Diff spans **more than one** API root under `Apis/`
  - **Do not** pre-check when **all** paths are under a **single** API root and nowhere else (that is API-local only).

- [x] `Legacy zone change (controlled sync only)`
  - **Pre-check** when **any** changed path is under `Legacy/` ([`CHARTER.md`](../../../CHARTER.md)).
  - Mixed Legacy + non-Legacy: still pre-check this when `Legacy/` is touched; apply **API-local** / **Cross-cutting** rules for the rest of the diff.

**Tests** — Allowed combinations:

- Unit tests only
- Integration tests only
- **Both** unit and integration tests
- **Neither** — `Not applicable` (only if rules below allow it)

**Exclusivity**

- **`Not applicable`** must **not** be pre-checked if **either** Unit or Integration rules match.
- Evaluate Unit and Integration **independently**; then evaluate `Not applicable` only if **neither** matched (and N/A safety rule passes).

- [x] `Unit tests`
  - **Pre-check** when `*Tests.cs`, `*Test.cs`, or paths under `**/Tests/**` were added or modified.
  - Otherwise leave unchecked; then consider `Not applicable`.

- [x] `Integration tests`
  - **Pre-check** when integration-style test projects or files changed (naming conventions as in this repo).
  - Otherwise leave unchecked; then consider `Not applicable`.

- [x] `Not applicable`
  - **Pre-check** only when **all** of the following hold:
    - Neither the Unit rule nor the Integration rule matched (no matching test artifacts in the diff).
    - The change plausibly needs **no** test updates (e.g. docs-only, governance/template-only, comments-only, other non-runtime edits).
  - **Do not** pre-check when **production or shared runtime code** under `Apis/` or `Platform/` changed but **no** test paths appear in the diff — leave `Not applicable` unchecked and use **#### Test Notes** to say tests were not seen in the diff and may still be required.
  - When you **do** pre-check `Not applicable`: infer a one-line rationale from the diff; put it in **#### Test Notes** (replace the *italic* placeholder); ask the user to verify.

**Checklist** (template **Checklist** section)

- [ ] `I have performed a self-review` — Never auto-check unless the user explicitly asked the skill to assert it.

- [ ] `I have kept the PR focused (no unrelated changes)` — Never auto-check unless the user explicitly asked the skill to assert it.

- [x] `I have added/updated tests where appropriate`
  - **Pre-check** when the Unit or Integration test path rules above matched.
  - **Do not** pre-check when **Tests** `Not applicable` is pre-checked (N/A is not an “added/updated tests” attestation).
  - Otherwise leave unchecked unless the user explicitly asked the skill to assert it.

- [ ] `If this is cross-cutting, I have highlighted it explicitly in the description`
  - Never auto-check (self-attestation).
  - If **Impact** cross-cutting would be pre-checked by path rules: remind the user to state cross-cutting impact in **#### Context** and to check this box after editing.

- [ ] `If this touches Legacy/, this is a deliberate sync (not feature work)`
  - Never auto-check (self-attestation).
  - If any path is under `Legacy/`: remind the user to confirm deliberate sync in the description and to check this box after editing.

- [x] `PR title follows the required format (see COMMIT_CONVENTIONS.md at repo root)`
  - **Pre-check** when the skill built the title per `COMMIT_CONVENTIONS.md` (verbatim template line).

**Branch name** (not a template checkbox; confirm when reporting — see Step 7)

- Shape: `issue-ref-short-description` with **lowercase kebab-case** `short-description`.
- **No** grouping prefixes (`feature/`, `bugfix/`, `chore/`, etc.).
- `issue-ref` matches tracker casing (e.g. `ABC-233`, `OPS-1234`).

**Issue link patterns** (best guess — user must verify URL and key)

- **Jira** (project keys supported by your workspace) — `[KEY-N](https://jira.example.com/browse/KEY-N)`
  Examples: `[ABC-233](https://jira.example.com/browse/ABC-233)`, `[OPS-1234](https://jira.example.com/browse/OPS-1234)`
- `noissue` — state `noissue` and a short reason in the Issue section.

Always remind the user to **verify the issue link** on the PR.

### Step 5: Prepare branch for review

This repository does **not** prescribe a rebase-and-squash workflow. The goal is a **reviewable** branch: reasonably current against the target branch, conflict-free, and pushed when you open the PR.

- Ensure the branch is in a **reviewable** state and **reasonably up to date** with the target branch (`<base>` from Step 1, usually `main`). `git fetch` then compare or integrate as needed.
- **Prefer rebasing** onto the latest target branch (`origin/<base>`) **where practical** so the PR diff stays current and clean.
- If rebasing is **not** appropriate (shared branch, user preference, or team workflow), update using the **repository’s normal workflow** (e.g. merge `origin/<base>` into your branch). Confirm with the user when unsure.
- **Resolve conflicts** before opening the PR.
- **Do not assume** a squash merge when writing the PR; **merge strategy** is set by **repository policy** and **reviewers**.
- **Working tree** should be clean for the changes you intend to include (no stray uncommitted edits).

Push happens in Step 6 — do not leave the branch only local if the user asked for a PR.

### Step 6: Push and create PR

- **GitHub** — open the PR with **GitHub MCP** only; do **not** use `gh` unless the user explicitly asks.

**Push** — per **Shell Compatibility** (below): **one Shell tool call** per command; do **not** chain with `&&`, `||`, or `;`:

1. `git push -u origin HEAD` (or push the current branch name if already tracking).
2. If the remote rejects (non-fast-forward): work with the user — integrate remote updates (merge or rebase per their workflow), or use `git push --force-with-lease origin HEAD` **only** when they intentionally rewrote history. Do not force-push without explicit agreement.

**Create draft PR** — GitHub MCP tool **`create_pull_request`** (read the tool schema first). Typical fields:

- `owner`, `repo` — from `git remote get-url origin` or workspace context
- `title` — Step 3
- `body` — full markdown from Step 4
- `head` — current branch name
- `base` — Step 1 base (usually `main`)
- `draft` — `true`

If GitHub MCP is missing or fails: say so and ask the user to fix MCP — do not silently use `gh` unless they requested it.

### Step 7: Report to user

Print concisely:

1. Clickable link: `[#PR_NUMBER - PR_TITLE](PR_URL)`
2. Reminder: verify issue link, finish Impact and Tests, complete checklist
3. **Branch name** — confirm it matches [`BRANCHING.md`](../../../BRANCHING.md) (`issue-ref-short-description`, kebab-case, no grouping prefixes); flag mismatches for the user
4. If the user **rebased or force-pushed**: remind anyone who had already pulled the branch to re-sync (reset or re-fetch per their workflow)
5. If the description was auto-generated: ask whether they want to refine it

## Shell Compatibility

Shell commands run in **cross-platform PowerShell** (`pwsh.exe`), not Windows PowerShell (`powershell.exe`) or bash.

- One **Shell tool call** per command — do **not** chain with `&&`, `||`, or `;`. This is **agent / tooling policy** so each command’s outcome is clear and logs map cleanly to a single invocation; `pwsh` allows `;` between statements, but chaining is avoided here on purpose.
- Prefer **`Remove-Item`** for deletes in skill-driven commands. In `pwsh`, `rm` is an alias for `Remove-Item`; using the cmdlet name avoids ambiguity for readers and logs.
- Avoid bash-only syntax (e.g. `$(...)` subshells).

## Key Requirements

- Create PRs as **drafts** (`draft: true` on `create_pull_request`).
- Read `.github/pull_request_template.md` from the repo root each time.
- PR title pattern: `<issue-ref>: <type>(<scope>): <description>` — must match `COMMIT_CONVENTIONS.md` and `.github/pull_request_template.md` (allowed types/scopes from `COMMIT_CONVENTIONS.md`).
- Use **GitHub MCP** for create/update PR flows, not `gh`, unless the user explicitly requests `gh`.
- Aim for a **reviewable** branch (up to date with target, conflicts resolved); **do not** assume squash merge — merge strategy follows repo policy and reviewers.
- Auto-check only checklist items the skill can verify; leave the rest for the user.
- Always remind the user to verify the issue link.
