# useful-ai-skills

Local collection of reusable Cursor skill packs for common engineering workflows.
Each skill lives in its own folder and provides a focused `SKILL.md` prompt with
optional templates and reference files. This README is the index for the current
skill set in this repository.

## Skills Catalog

- [`ado-work-items/SKILL.md`](ado-work-items/SKILL.md) - Interact with Azure DevOps REST API to query work items, create bugs/tasks, and transition work through Kanban states.
- [`adr-template/SKILL.md`](adr-template/SKILL.md) - Produce Architecture Decision Records (ADRs) with clear context, options, trade-offs, and consequences.
- [`automation-scaffolder/SKILL.md`](automation-scaffolder/SKILL.md) - Scaffold automation specs with triggers, inputs, execution steps, safeguards, and observability requirements.
- [`crit-think/SKILL.md`](crit-think/SKILL.md) - Challenge assumptions and encourage critical thinking to ensure the best possible solution and outcomes.
- [`demo-understanding/SKILL.md`](demo-understanding/SKILL.md) - Validate user understanding of code, design patterns, and implementation details through guided questioning.
- [`dependency-security-maintenance/SKILL.md`](dependency-security-maintenance/SKILL.md) - Run periodic dependency and security maintenance with repeatable checks, upgrade planning, and risk tracking.
- [`dotnet-expert-se/SKILL.md`](dotnet-expert-se/SKILL.md) - Provide expert .NET software engineering guidance using modern software design patterns.
- [`dotnet-fs-mentor/SKILL.md`](dotnet-fs-mentor/SKILL.md) - Opinionated mentor for .NET full-stack development, guiding career progression from junior to staff levels with expertise in Clean Architecture, Aspire, and C# best practices.
- [`gh-actions-expert/SKILL.md`](gh-actions-expert/SKILL.md) - GitHub Actions specialist focused on secure CI/CD workflows, action pinning, OIDC authentication, permissions least privilege, and supply-chain security.
- [`gh-commit/SKILL.md`](gh-commit/SKILL.md) - Create basic git commits with repository conventions: issue-first Conventional Commits headers, mandatory scopes, and focused atomic changes.
- [`gh-draft-pr/SKILL.md`](gh-draft-pr/SKILL.md) - Create a draft pull request on GitHub by gathering branch context, filling in the repository PR template, pushing the branch, and creating the PR via GitHub MCP.
- [`gh-pull-request/SKILL.md`](gh-pull-request/SKILL.md) - Create GitHub pull requests with repository conventions: Conventional Commits titles, issue-first branch names, and the standard PR template body.
- [`github-issues/SKILL.md`](github-issues/SKILL.md) - Interact with GitHub Issues to search, create, update, and manage issue state using GitHub API/CLI.
- [`issue-triage-pack/SKILL.md`](issue-triage-pack/SKILL.md) - Triage new work into consistent issue templates for bugs, features, tech debt, and incident follow-ups.
- [`jira-int/SKILL.md`](jira-int/SKILL.md) - Interact with Jira Server REST API to query issues, create tickets, and transition work items for a Kanban project.
- [`localise-markdown/SKILL.md`](localise-markdown/SKILL.md) - Localise documentation and README markdown to a target locale: New Zealand English spelling, Te Reo Maori orthography, or full Te Reo translation when requested.
- [`mentor/SKILL.md`](mentor/SKILL.md) - Help mentor the engineer by providing guidance and support.
- [`postmortem/SKILL.md`](postmortem/SKILL.md) - Create blameless postmortems for incidents with timeline, root cause, customer impact, and corrective actions.
- [`pr-review-checklist/SKILL.md`](pr-review-checklist/SKILL.md) - Review pull requests with a deterministic checklist for correctness, risk, tests, and maintainability.
- [`project-documenter/SKILL.md`](project-documenter/SKILL.md) - Generates professional MS Word project documentation with draw.io architecture diagrams and embedded PNG images.
- [`release-notes/SKILL.md`](release-notes/SKILL.md) - Generate release notes from git history, pull requests, and changelog inputs using a consistent publish-ready format.
- [`runbook-templates/SKILL.md`](runbook-templates/SKILL.md) - Generate cross-platform runbooks for local development, build and test, and deployment operations.
- [`senior-software-engineer/SKILL.md`](senior-software-engineer/SKILL.md) - Provide principal-level software engineering guidance with focus on engineering excellence, technical leadership, and pragmatic implementation.
- [`simplify-architecture/SKILL.md`](simplify-architecture/SKILL.md) - Simplify technical markdown into plain-language, high-level architecture documentation for non-technical readers.
- [`tutor/SKILL.md`](tutor/SKILL.md) - Tutors while reviewing work, explains concepts, ties feedback to principles, and checks understanding with brief questions.

## How to Use These Skills

- Reference a skill by folder (for example `gh-pull-request`) and follow its `SKILL.md` instructions.
- Edit each skill's `description` in front matter to keep catalog summaries accurate.
- Keep each skill folder self-contained with `SKILL.md` at the root and optional `templates/` or `reference.md`.

## Maintenance

- When adding a new skill folder, add one matching catalog entry in alphabetical order.
- Verify the README link points to an existing `*/SKILL.md` path before committing.
- Keep descriptions to one line so the catalog stays easy to scan.