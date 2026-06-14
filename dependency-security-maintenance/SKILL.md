---
name: dependency-security-maintenance
description: >-
  Run periodic dependency and security maintenance with repeatable checks,
  upgrade planning, and risk tracking. Use when the user asks for monthly
  maintenance, vuln remediation, or dependency refresh planning.
---

# Dependency and Security Maintenance

Standardize routine updates and vulnerability remediation.

## When to Apply

- User asks for dependency update cadence workflows
- User asks for vulnerability triage and patch plans
- User asks for a monthly maintenance checklist

## Workflow

### 1. Inventory and scan

- Package inventory by ecosystem
- Vulnerability scan results
- Outdated/unsupported package list

### 2. Prioritize and plan

- Patch critical/high vulnerabilities first
- Separate safe minor/patch upgrades from risky major upgrades
- Capture rollback plan for risky updates

### 3. Execute and verify

- Update in small batches
- Run tests/build/security scans after each batch
- Document deferred items with reasons

Use `templates/monthly-maintenance.md`.

## Output Mode

Return:

- `Maintenance report markdown`
- `Upgrade plan`
- `Deferred risks`

