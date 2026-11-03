# Observability and deployment

Three signals answer different questions:

- **Logs:** what happened, with context.
- **Metrics:** how often and how much.
- **Traces:** where time went across boundaries.

Include request ID, route, status, duration, and safe identifiers. Never log
passwords, tokens, or unnecessary personal data.

## Safe deployment sequence

1. Make schema changes backward-compatible.
2. Deploy code that can work with both schema versions.
3. Migrate or backfill in bounded batches.
4. Enable new behavior behind a reversible switch when risk is high.
5. Monitor error rate, latency, saturation, and business signals.
6. Roll back code or disable behavior; do not assume a schema rollback is safe.
