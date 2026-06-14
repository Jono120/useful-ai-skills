# Cursor Skills

`Cursor-Skills/` contains the canonical source skill definitions for this repository.
All downstream variants should be derived from the content in this folder.

## Purpose

- Provide reusable, focused instruction packs for common engineering workflows.
- Keep operational guidance close to examples and templates.
- Serve as the source of truth for skill behaviour and constraints.

## Conventions

- Each skill lives in `Cursor-Skills/<skill>/`.
- Required file: `SKILL.md`.
- Optional files: `templates/*.md` and `reference.md`.
- Keep instructions concise and preserve explicit constraints.
- Prefer deterministic steps and concrete output expectations.

## Structure Expectations

- One domain capability per skill folder.
- Supporting files stay local to the skill folder.
- Folder names are stable because they map 1:1 to `Github-Copilot-Skills/`.

## `SKILL.md` Authoring Guidance

- Include clear trigger phrases in the description (`Use when...`).
- Define scope boundaries (`USE FOR` / `DO NOT USE FOR`) when applicable.
- Use actionable instructions in ordered steps for repeatability.
- Specify expected outputs so results are reviewable.
- Keep examples realistic and aligned with the repository toolchain.

## Templates and References

- Put reusable deliverables in `templates/` (checklists, ADR skeletons, runbooks, etc.).
- Put stable supporting context in `reference.md` (definitions, taxonomy, standards).
- Reference local files with relative paths and keep file names descriptive.

## Maintenance Workflow

- When updating a skill, verify `SKILL.md` and any local templates stay consistent.
- Keep skill folder names unchanged unless a migration is explicitly planned.
- Add new skills in a new folder with `SKILL.md` plus optional support files.
- If a skill is deprecated, remove or archive the entire folder in one change.

## Quality Checklist

- `SKILL.md` exists and matches the folder intent.
- Instructions are specific, testable, and free of placeholder ambiguity.
- Optional files referenced by the skill actually exist.
- Descriptions remain short, accurate, and outcome-oriented.
