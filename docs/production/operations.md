# Observability and deployment

Three signals answer different questions:

- **Logs:** what happened, with context.
- **Metrics:** how often and how much.
- **Traces:** where time went across boundaries.

Include request ID, route, status, duration, and safe identifiers. Never log
passwords, tokens, or unnecessary personal data.

## Useful service-level indicators

- **Availability:** proportion of requests that succeed.
- **Latency:** proportion of requests below the target, preferably by
  percentile rather than average.
- **Errors:** application failures and dependency failures separately.
- **Saturation:** CPU, memory, database connections, queue depth, and disk.

An alert should identify a user-visible symptom, include enough context to
start diagnosis, and have a documented response. Alerting on every exception
creates noise instead of reliability.

## Safe deployment sequence

1. Make schema changes backward-compatible.
2. Deploy code that can work with both schema versions.
3. Migrate or backfill in bounded batches.
4. Enable new behavior behind a reversible switch when risk is high.
5. Monitor error rate, latency, saturation, and business signals.
6. Roll back code or disable behavior; do not assume a schema rollback is safe.

## Debugging sequence

1. Confirm the impact and time window.
2. Check recent deploys, migrations, configuration, and dependency health.
3. Compare a failing request with a successful one using its request ID.
4. Mitigate first: disable a feature, shed load, or roll back.
5. Preserve evidence before changing the system.
6. Record the cause and preventive action in a postmortem.

## Instrumentation boundaries

Instrument at request entry, database calls, queue operations, and outbound
network calls. Carry a correlation ID across those boundaries, but do not
assume it proves causality when asynchronous work is involved; record a
causation ID and job/event ID as well.

Metrics should have bounded labels. A user ID, URL, or raw exception message
can create an unbounded time-series cardinality problem. Put high-cardinality
detail in sampled logs or traces instead.

## Health checks

Separate liveness from readiness. Liveness answers “should this process be
restarted?” Readiness answers “should this process receive traffic?” A
liveness check that depends on the database can restart every process during a
database outage and make recovery worse.

## Safe operations

Use feature flags, canaries, bounded migrations, and rollback commands that
are rehearsed before an incident. A rollback is not safe if the new release
has already written data the old release cannot read. Check compatibility
before deploying and include the verification query in the runbook.

## Worked deployment example

For adding `users.display_name`:

```text
Release 1: add nullable column
Release 2: write old and new representations
Backfill: update bounded batches and monitor locks
Release 3: read new representation
Release 4: stop writing old representation
Cleanup: remove old column after rollback window
```

Never deploy code that reads a column before the migration is complete, and
never remove a column while an older process may still query it. The rollback
plan must account for data written by the new release.
