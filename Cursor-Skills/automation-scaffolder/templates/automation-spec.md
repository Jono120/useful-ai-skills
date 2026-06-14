# Automation Spec: <name>

## Objective
<What this automation should achieve>

## Trigger
- Type: <event|schedule|manual>
- Source: <system/user/action>
- Frequency/SLA: <value>

## Inputs and Dependencies
- Inputs: <data/contracts>
- External dependencies: <APIs/services>
- Permissions/secrets: <requirements>

## Workflow
1. <step>
2. <step>
3. <step>

## Failure Handling
- Retries: <policy>
- Dead-letter/escalation: <policy>
- Human intervention path: <who/how>

## Safeguards
- Idempotency strategy: <approach>
- Rate/concurrency limits: <rules>
- Approval gates: <if any>

## Observability
- Logs: <key events>
- Metrics: <SLO signals>
- Alerts: <conditions + routing>

## Rollout and Rollback
- Rollout plan: <phased/canary/full>
- Rollback plan: <exact steps>

## Ownership
- Team: <owner>
- On-call/support: <model>

## Readiness Checklist
- [ ] Inputs and contracts validated
- [ ] Failure modes tested
- [ ] Alerts verified
- [ ] Rollback rehearsed

