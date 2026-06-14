# Localisation reference (en-NZ and Te Reo Māori)

Use this file when applying `localize-markdown`. Prefer consistency over exhaustive replacement—do not change established product names or trademarks.

---

## New Zealand English (en-NZ)

NZ English follows **British spelling** with some NZ-preferred terms. Apply to **prose only** (see SKILL.md protected regions).

### Spelling patterns

| US / avoid | NZ / prefer | Notes |
|------------|-------------|--------|
| organize, organization | organise, organisation | `-ise` not `-ize` |
| realize, recognize | realise, recognise | |
| analyze | analyse | |
| color | colour | |
| behavior | behaviour | |
| center | centre | Noun/adj.; "center" as verb is rare in docs |
| meter (measurement) | metre | Keep **meter** for devices (smart meter) if industry-standard |
| license (noun) | licence | Noun; **license** as verb is correct in NZ English |
| defense | defence | |
| catalog | catalogue | |
| program (general) | programme | Exception: **computer program** |
| traveling, canceled | travelling, cancelled | Double **l** where UK doubles |
| gray | grey | |
| fulfill | fulfil | |

### Vocabulary (optional, context-dependent)

| US / generic | NZ-preferred | When to use |
|--------------|--------------|-------------|
| cell phone | mobile | Informal docs |
| vacation | holiday | |
| math | maths | |
| sidewalk | footpath | |
| truck | truck / ute | Keep **truck** in technical/logistics contexts |

### Māori terms in English prose

When English documentation names concepts that are commonly expressed in Te Reo, use standard NZ forms **with macrons**:

| Avoid | Prefer |
|-------|--------|
| Maori | Māori |
| whanau | whānau |
| whenua (if already Māori, check macron) | whenua |
| Aotearoa | Aotearoa (no macron on all vowels—correct as shown) |
| Te Reo | Te Reo Māori (or **te reo Māori** in running text) |

Do not translate established English product UI labels unless the user requests UI localisation.

---

## Te Reo Māori orthography (mi-spelling)

Apply when correcting **Māori words** within English or bilingual markdown.

### Macrons (tohutō)

Long vowels are marked with macrons: **ā, ē, ī, ō, ū** (uppercase: **Ā, Ē, Ī, Ō, Ū**).

Common documentation terms:

| Common error | Correct |
|--------------|---------|
| Maori | Māori |
| whanau | whānau |
| kaupapa (verify) | kaupapa |
| tamariki | tamariki |
| iwi | iwi |
| hapu | hapū |
| rohe | rohe |
| taonga | taonga |
| kaitiaki | kaitiaki |
| mana whenua | mana whenua |
| Aotearoa | Aotearoa |

### Rules

1. **Māori** — Always capitalise when referring to the language or people.
2. **Macrons change meaning** — e.g. **keke** (cake) vs **kēkē** (chicken). If unsure, flag for human review; do not invent macrons on placenames without a trusted source.
3. **Loanwords in English** — Words adopted into NZ English (e.g. **kia ora** in informal docs) still take macrons when written as Māori.
4. **Do not macronise English** — e.g. leave "New Zealand" unchanged.

### Names and placenames

- Use official spellings from [NZGB Gazetteer](https://gazetteer.linz.govt.nz/) or project-approved glossary when localising place names.
- If the repo already uses a macron-less official form (legacy export), note the inconsistency; do not silently "fix" without user approval.

---

## Full Te Reo translation (mi-NZ)

Use when the user explicitly requests **translation**, not spelling-only.

### Keep in English (unless glossary says otherwise)

- Code, configuration, CLI examples
- API paths, HTTP methods, status codes
- Type names, class names, database columns
- Brand and product names registered in English
- Most acronyms (define once in Te Reo if needed: "API (Application Programming Interface)")

### Translate

- Headings, introductory paragraphs, procedural steps
- Alt text, figure captions
- Error-message **descriptions** in docs (not the literal `throw` string in code samples)

### Register

- Technical documentation: clear, respectful, **formal** (avoid slang)
- Use **kupu hou** (loan transliterations) only when established in NZ government or education corpora; otherwise prefer a short English term in parentheses on first use

### Bilingual pattern (optional)

When the user wants both languages:

```markdown
## Whakamārama / Description

[Te Reo paragraph]

[English paragraph]
```

---

## Glossary template (per project)

If the repo has no glossary, offer to create `.github/docs-locale-glossary.md`:

| English term | en-NZ note | Te Reo (if used) | Do not translate |
|--------------|------------|------------------|------------------|
| deployment | | whakaputa (context-dependent) | |
| pull request | | | yes (GitHub UI) |

---

## Quality checks

**en-NZ scan** (prose): `organize|color|center|license\b(?!d)|defense|catalog\b(?!ue)`

**Māori scan** (prose): `\bMaori\b`, `\bwhanau\b`, `\bhapu\b` without macrons where reference lists macrons

After edits, re-read changed sections for broken markdown (unclosed fences, broken link labels).
