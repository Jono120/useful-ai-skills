---
mode: agent
name: localize-markdown
description: Localise documentation and README markdown to a target locale: New Zealand English spelling, Te Reo Māori orthography (macrons), or full Te Reo translation when requested. Preserves code, URLs, and technical identifiers. Use when the user asks to localise, translate, or fix spelling in docs, README, or markdown for en-NZ, NZ English, Te Reo, or Māori.
---

# GitHub Copilot Skill: localize-markdown

You are GitHub Copilot. Use this skill when: Localise documentation and README markdown to a target locale: New Zealand English spelling, Te Reo Māori orthography (macrons), or full Te Reo translation when requested. Preserves code, URLs, and technical identifiers. Use when the user asks to localise, translate, or fix spelling in docs, README, or markdown for en-NZ, NZ English, Te Reo, or Māori.

## Copilot Operating Rules

- Follow user intent and repository conventions.
- Ask brief clarifying questions only when required.
- Prefer minimal, high-confidence changes.
- Validate outputs and report assumptions/limits.
- Keep responses concise and action-oriented.

## Skill Guidance

Convert `*.md` documentation (README, guides, ADRs) to the user's target locale while preserving structure, links, and technical content.

## When to Apply

- User asks to localise, translate, or anglicise/NZ-ise documentation
- User specifies **New Zealand English**, **en-NZ**, **Te Reo**, **Māori spelling**, or **macrons**
- User wants README or docs updated for a NZ audience
- User asks to fix Māori orthography (missing macrons, wrong spellings)

## Modes

Confirm the mode if the user did not specify it:

| Mode | Code | What changes |
|------|------|----------------|
| **NZ English** | `en-NZ` | British/NZ spelling and NZ-preferred vocabulary in English prose |
| **Māori orthography** | `mi-spelling` | Correct Te Reo Māori spellings and macrons in Māori terms embedded in English (or bilingual) docs |
| **Te Reo translation** | `mi-NZ` | Translate narrative prose to Te Reo Māori; keep code/APIs in English unless user says otherwise |

Default when ambiguous: **en-NZ** for general "localise docs"; ask before **mi-NZ** full translation.

## Workflow

### 1. Scope the work

Identify target files (paths the user named, or discover with search):

```bash
# Example: list markdown in repo root and docs/
git ls-files '*.md' 2>/dev/null || find . -name '*.md' -not -path '*/node_modules/*'
```

Note:

- Target locale/mode (`en-NZ`, `mi-spelling`, `mi-NZ`)
- Whether to update files in place or produce a summary/diff for review
- Project glossary or `reference.md` in this skill folder for consistent terms

### 2. Parse and protect non-prose regions

**Do not alter** content inside:

- Fenced code blocks (` ``` `)
- Inline code (`` ` ``)
- URLs and link targets (`](...)`, `<http...>`)
- HTML comments used as template instructions (unless user asks to remove them)
- YAML/TOML frontmatter **keys** and enum values; localise **values** only when they are human-facing display strings
- File paths, CLI commands, environment variable names, JSON keys, HTTP headers, NuGet/npm package names
- Issue/ticket IDs (`ABC-123`), semver, hex, UUIDs

**May localise** when clearly prose:

- Headings, paragraphs, list items, table cells (non-code)
- Image `alt` text
- Link **display** text (not the URL)

### 3. Apply locale rules

Use [reference.md](reference.md) for spelling tables and Māori orthography rules.

**en-NZ highlights:**

- Prefer `-ise` / `-isation` (organise, realise), `-our` (colour, behaviour), `-re` (centre, metre), `-ogue` (catalogue)
- NZ terms where appropriate: **whānau**, **whenua**, **Aotearoa** (with macrons when referring to Te Reo concepts in English text—see reference)

**mi-spelling highlights:**

- Restore macrons: ā, ē, ī, ō, ū (and lowercase)
- Use **Māori** not "Maori" when referring to the language/people in prose
- Do not guess macrons on proper names; prefer authoritative sources or leave a `[verify macron]` note

**mi-NZ translation highlights:**

- Natural, formal register suitable for technical documentation unless user requests conversational tone
- Keep untranslated: code, APIs, product names, most acronyms (expand on first use if helpful)
- Offer bilingual headings only if the user asked

### 4. Produce output

**In-place edit** (default when user says "update the README"):

- Edit only the scoped files
- Preserve existing heading anchors where possible (slug stability)
- Do not reformat unrelated whitespace or reorder sections

**Review-only** (when user says "suggest" or "draft"):

- Show a concise change summary: locale, files, rule categories applied
- List uncertain terms (glossary gaps, macron verification)

### 5. Validate

Before finishing:

- [ ] Code blocks and inline code unchanged (byte-for-byte unless typo inside a comment the user flagged)
- [ ] URLs and paths unchanged
- [ ] Frontmatter structure intact
- [ ] No accidental US spellings left in `en-NZ` mode (spot-check: organize, color, center, license as verb)
- [ ] Māori words in scope have consistent macron use per reference
- [ ] Technical accuracy preserved (commands still run, links still valid)

## User prompts to clarify

Ask only when blocking:

1. **Locale/mode** — en-NZ vs mi-spelling vs full mi-NZ translation?
2. **Files** — single file, `docs/`, entire repo?
3. **Glossary** — project-specific terms that must stay English (e.g. product codenames)?
4. **Commit** — should changes be committed? If yes, use `gh-commit` with `docs(repo):` or appropriate scope.

## Examples

**en-NZ** — Input sentence:

> The organization centralized its color palette and licensed the SDK.

Output:

> The organisation centralised its colour palette and licensed the SDK.

**mi-spelling** — Input:

> This guide supports Maori land data and whanau access.

Output:

> This guide supports Māori land data and whānau access.

**mi-NZ** (excerpt, narrative only) — User requests translation of a short intro paragraph; APIs stay English.

## Additional resources

- Spelling and macron reference: [reference.md](reference.md)
- NZ government style (supplementary): [Te Taura Whiri — orthographic guidelines](https://www.tetaurawhiri.govt.nz/en/te-reo-maori/orthography/)
