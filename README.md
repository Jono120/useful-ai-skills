This repository maintains two mirrored ecosystems:
- `Cursor-Skills/` with source `SKILL.md` definitions.
- `Github-Copilot-Skills/` with converted `COPILOT.prompt.md` prompts.

## Repository Purpose

- Provide reusable, task-focused prompt packs for common software delivery work.
- Keep skill instructions, templates, and references versioned with the repository.
- Support cross-platform prompt portability between Cursor and GitHub Copilot.
- Enable consistent authoring standards and repeatable skill maintenance.

## Structure

- Source: `Cursor-Skills/<skill>/SKILL.md`
- Target: `Github-Copilot-Skills/<skill>/COPILOT.prompt.md`
- Optional support files are mirrored per skill: `templates/*.md` and `reference.md`

## Tooling and Workflow

- **Markdown-first assets**: skills and supporting documentation are plain markdown files.
- **Mirrored folders**: each skill folder name is shared across both ecosystems.
- **Prompt normalisation**: Copilot prompts use a standardised format with required sections.
- **Validation checks**: parity checks confirm matching skill counts and support files.
- **Repository linting**: run lint/quality checks after substantive documentation updates.

## Compatibility and Mapping Guidance

- Keep folder names identical across both ecosystems.
- Preserve source intent and constraints during conversion.
- Apply minimal wording changes unless platform-specific wording is required.
- Validate parity by counting source `SKILL.md` and target `COPILOT.prompt.md`.

## Contribution Guidance

- Add new skills in `Cursor-Skills/` first as the canonical source.
- Include `templates/` and `reference.md` only when the skill requires them.
- Keep descriptions concise, explicit, and outcome-oriented.
- After source changes, re-generate and verify matching Copilot prompts.
- Update relevant README files when conventions or workflow expectations change.

## Ecosystem READMEs

- Cursor conventions: `Cursor-Skills/README.md`
- Copilot conventions and usage: `Github-Copilot-Skills/README.md`