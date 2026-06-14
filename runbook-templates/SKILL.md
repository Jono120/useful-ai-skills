---
name: runbook-templates
description: >-
  Generate cross-platform runbooks for local development, build and test, and
  deployment operations. Use when the user asks for operational runbooks,
  onboarding steps, or standard operating procedures.
---

# Runbook Templates

Create predictable runbooks that work across environments.

## When to Apply

- User asks for step-by-step operational docs
- User asks to standardize onboarding/build/deploy instructions
- User asks for platform-specific command guidance

## Workflow

### 1. Select runbook type

- Local development -> `templates/local-dev.md`
- Build and test -> `templates/build-test.md`
- Deployment -> `templates/deploy.md`

### 2. Populate cross-platform instructions

- Prefer command pairs where shell differences matter
- Include prerequisites, validation, and rollback/escape paths

### 3. Validate usability

- Steps are executable in order
- Environment assumptions are explicit
- Verification criteria are objective

## Output Mode

Return:

- `Runbook markdown`
- `Prerequisites checklist`
- `Known environment caveats`

