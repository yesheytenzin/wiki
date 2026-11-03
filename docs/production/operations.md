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
