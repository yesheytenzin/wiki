# Reliability and incident response

Reliability means the service behaves acceptably for users over time. Define
“acceptable” with service-level indicators and targets before choosing
infrastructure.

## Failure budget

If the target is 99.9% availability, the remaining 0.1% is the error budget.
The budget makes trade-offs explicit: repeated budget exhaustion should slow
risky changes and prioritize reliability work.

## Recovery design

For each critical dependency, document:

- how failure is detected;
- whether the request retries, degrades, or fails;
- what data may be lost;
- how to restore service;
- how to verify recovery.

Backups are only useful when restoration is tested. Define recovery point
objective (how much data may be lost) and recovery time objective (how long
restoration may take), then exercise the procedure.

During an incident, stabilize first, communicate impact and next update time,
preserve evidence, and write a blameless postmortem afterward.

## SLO design

Choose indicators that reflect user experience: successful requests, useful
responses, completion time, and freshness. Set targets by critical operation,
not only by service average. A fast health check does not prove that checkout
or publishing content works.

Use error budgets to make change decisions. A budget is not permission to
spend errors carelessly; it is a shared signal for balancing feature delivery
and reliability work.

## Runbooks

A runbook should state symptoms, dashboards, safe diagnostic commands,
mitigations, escalation contacts, and verification steps. Include what not to
do, such as restarting every worker before preserving evidence.

Practice the runbook with a game day. Inject a dependency timeout, queue
backlog, expired certificate, or database failover and measure time to detect,
mitigate, and restore.

## Data recovery

Define recovery point and recovery time objectives for each data class. Test
restores in an isolated environment, verify row counts and application
behavior, and document how to prevent the restored system from sending
duplicate external effects.

## Worked incident exercise

Scenario: queue age rises from 30 seconds to 20 minutes after a provider
starts timing out.

Expected response:

1. Confirm user impact and stop optional producers.
2. Check provider latency, worker concurrency, retry rate, and dead letters.
3. Reduce retry pressure with backoff or disable the provider integration.
4. Preserve failed payload identities and request IDs.
5. Drain or replay only after the provider is healthy.
6. Write a postmortem with an alert on queue age and provider saturation.

The exercise is complete only when the metric, mitigation, and verification
step are written into a runbook.
