---
name: postmortem
description: >-
  Create blameless postmortems for incidents with timeline, root cause,
  customer impact, and corrective actions. Use when the user asks for an
  incident retrospective, outage report, or lessons-learned document.
---

# Postmortem

Write clear, blameless incident analysis focused on learning and prevention.

## When to Apply

- User asks to draft a postmortem after an incident
- User needs a standardized incident report for stakeholders
- User asks to convert incident notes into a formal document

## Workflow

### 1. Establish factual timeline

- Detection time
- Escalation milestones
- Mitigation and recovery

### 2. Analyze root cause and contributing factors

- Distinguish trigger from underlying systemic factors
- Avoid person-blame phrasing

### 3. Define actions with ownership

- Corrective and preventive actions
- Owner and due date for each action

Use `templates/postmortem.md`.

## Output Mode

Return:

- `Postmortem markdown`
- `Action items list`
- `Unknowns needing follow-up`

