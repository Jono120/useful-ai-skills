---
name: simplify-architecture
description: >-
  Simplify technical markdown into plain-language, high-level architecture
  documentation for non-technical readers. Strips implementation detail, uses
  C4 Context/Container framing, and optional diagrams. Use when the user asks
  to simplify docs, explain architecture to stakeholders, executive summary,
  business audience, or high-level overview from README or technical markdown.
---

# Simplify Architecture Documentation

Transform technical `*.md` (README, architecture notes, ADRs, runbooks) into **high-level architecture** prose for readers who do not need implementation detail.

**Not the same as** `project-documenter`: that skill discovers the codebase and produces full engineer-facing docs and Word output. This skill **reworks existing markdown** (and optionally light codebase context) into a short, accurate, non-technical overview.

## When to Apply

- User asks to simplify, dumb down, or plain-language documentation
- Audience is executives, product owners, compliance, operations managers, or general staff
- User wants a **high-level architecture** summary from technical docs
- User asks for stakeholder-facing or "architecture for business" markdown

## Audience Assumptions

Readers:

- Understand business goals, users, and data in general terms
- Do **not** need class names, repos, frameworks, CI steps, or API field lists
- May need trust, privacy, and operational impact explained plainly

Write at roughly **Year 10 reading level**: short sentences, common words, define unavoidable jargon once.

## Modes

| Mode | Output | When |
|------|--------|------|
| **Derive** | New simplified doc from source markdown | Default |
| **Replace** | Overwrite source file | Only when user explicitly requests |
| **Side-by-side** | Simplified doc + short "what we removed" appendix | User wants audit trail for reviewers |

Default output path (unless user specifies):

`docs/architecture-overview.md`

## Workflow

### 1. Gather sources

Read the markdown the user named. If none named, discover candidates:

```bash
git ls-files '*.md' 2>/dev/null
```

Prefer, in order:

1. `ARCHITECTURE.md`, `docs/architecture*.md`
2. `README.md` (architecture-relevant sections only)
3. ADRs or design docs the user points to

Optionally skim the repo **only** when source markdown is thin or contradictory—enough to validate system boundaries, not to dump code into the output.

### 2. Extract facts (internal)

Before writing, list privately:

- **Purpose** — What problem does the system solve for users or the organisation?
- **Users** — Who interacts with it (people, other systems)?
- **Major parts** — 3–7 named capabilities or services (plain names, not project codenames unless already business terms)
- **Data** — What information enters, is stored, and leaves (categories, not schemas)
- **Dependencies** — Other systems, cloud regions, third parties (at integration level)
- **Risks / constraints** — Security, availability, legacy, regulatory (one line each)

Drop: file paths, NuGet/npm packages, Terraform modules, SQL, config keys, version numbers, sprint references.

### 3. Write using the output template

Follow [reference.md](reference.md) for section structure, C4 level, diagram rules, and vocabulary.

**C4 scope for this skill:**

| Include | Exclude |
|---------|---------|
| **Context** — system in the world, users, external systems | **Component** — classes, modules, folders |
| **Container** — major deployable parts and how they connect | Code samples, CLI, IaC snippets |
| Data flows in business terms | Sequence diagrams with HTTP status codes |

Target length: **1–3 pages** of markdown unless the user asks for longer.

### 4. Add optional visuals

Include **at most one** mermaid diagram when it clarifies relationships:

- Prefer `flowchart LR` or `C4Context`-style simple boxes
- Max 7 nodes; label with business names ("Customer portal", "Scan storage") or tool name

Skip diagrams if the source material is too vague—do not invent components.

### 5. Validate accuracy

- [ ] Every claim traceable to source markdown or verified repo fact
- [ ] No fabricated integrations or compliance claims
- [ ] Jargon defined on first use or replaced
- [ ] No code blocks unless the user asked to keep examples (default: remove)
- [ ] Links to deep technical docs preserved as "Further reading" if they exist

### 6. Deliver

**Derive mode** — write the new file; tell the user the path.

**Replace mode** — confirm once, then overwrite.

End with a **3-bullet executive summary** in chat (not necessarily in the file) so the user can paste into email or slides.

## Clarify only when blocking

1. **Source files** — which markdown to simplify?
2. **Output path** — default `docs/user-overview.md` or custom?
3. **Sensitivity** — redact names, regions, or vendor details?
4. **Diagrams** — include mermaid yes/no?

## Relationship to other skills

| Skill | Use when |
|-------|----------|
| `simplify-architecture` | Non-technical high-level architecture from existing md |
| `project-documenter` | Full technical project summary + Word + draw.io from codebase |
| `localize-markdown` | NZ English or Te Reo after content is final |

## Example

**Input** (technical excerpt):

> The `PointClouds.Api` hosts minimal APIs behind YARP. EF Core maps `ScanEntity` to PostgreSQL 16. OpenTelemetry exports to Azure Monitor via `Platform.Telemetry`.

**Output** (plain architecture):

> The **scan service** accepts requests from client applications, applies business rules, and stores scan metadata in a managed database. Operations teams monitor health and performance through the organisation's standard cloud monitoring tools.

## Additional resources

- Output template and word list: [reference.md](reference.md)
