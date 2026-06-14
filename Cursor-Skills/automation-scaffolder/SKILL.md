---
name: automation-scaffolder
description: >-
  Scaffold automation specs with triggers, inputs, execution steps, safeguards,
  and observability requirements. Use when the user asks to design a new
  automation workflow, job, bot, or scheduled task.
---

# Automation Scaffolder

Turn automation ideas into implementation-ready specifications.

## When to Apply

- User asks to design a new automation
- User asks to formalize manual process automation
- User asks for a workflow/job scaffold before coding

## Workflow

### 1. Define objective and trigger

- Business objective
- Trigger type (event, schedule, manual)
- Success criteria

### 2. Define flow and controls

- Inputs, dependencies, and permissions
- Step-by-step execution flow
- Failure handling and retry rules
- Guardrails (idempotency, rate limits, approvals)

### 3. Define operations and rollout

- Logging/metrics/alerts
- Rollout and rollback plan
- Ownership and support model

Use `templates/automation-spec.md`.

## Output Mode

Return:

- `Automation specification markdown`
- `Open design risks`
- `Implementation readiness checklist`

