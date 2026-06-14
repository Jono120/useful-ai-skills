# GitHub Copilot Skills

`Github-Copilot-Skills/` contains normalised prompt files for Copilot workflows.

## Purpose

- Provide production-ready prompt files tailored for GitHub Copilot usage.
- Keep each skill self-contained with clear instructions and expected outcomes.
- Maintain consistent prompt structure so skills are easier to review and reuse.

## Required Prompt Format

- Each skill lives in `Github-Copilot-Skills/<skill>/`.
- Required file: `COPILOT.prompt.md`.
- Optional files: `templates/*.md` and `reference.md`.
- Every `COPILOT.prompt.md` must use this frontmatter in this exact order:
  - `mode: agent`
  - `name: <skill name>`
  - `description: <skill description>`
- Every `COPILOT.prompt.md` must include these sections:
  - `# GitHub Copilot Skill: <name>`
  - `## Copilot Operating Rules`
  - `## Skill Guidance`

## Usage

- Choose the skill folder by name (for example `gh-pull-request`).
- Use `COPILOT.prompt.md` as the primary prompt content.
- Include local `templates/*.md` or `reference.md` when the prompt references them.

## Authoring Guidance

- Keep the description specific and trigger-oriented (`Use when...`).
- Write instructions as concise, ordered steps that Copilot can execute reliably.
- Prefer explicit constraints and output requirements over broad guidance.
- Keep examples practical and relevant to typical engineering workflows.
- Avoid platform-ambiguous wording when Copilot-specific language is clearer.

## Folder Conventions

- One skill per folder, centred on a single domain capability.
- Keep supporting artefacts local to the skill that uses them.
- Use descriptive template names that match expected deliverables.
- Keep prompt content in markdown only; avoid non-portable formatting.

## Quality Checklist

- `COPILOT.prompt.md` exists and follows the required format.
- Frontmatter keys are present and in the required order.
- Required sections are present and non-empty.
- Referenced local templates or reference files exist.
- Instructions are testable, specific, and free of placeholder ambiguity.

## Maintenance

- Update prompts when workflows, tooling, or conventions change.
- Re-check folder consistency after any batch edits.
- Keep wording concise and action-oriented for Copilot execution.
