# GraphQL testing and operations

Test GraphQL at four levels:

1. **Schema checks:** the schema builds, types are valid, and breaking
   changes are detected.
2. **Resolver tests:** authorization, input errors, and domain calls behave
   correctly.
3. **Operation tests:** representative queries and mutations return the
   documented shape.
4. **System tests:** database, authentication, batching, timeouts, and
   downstream failures work together.

## Contract testing

Keep a registry of important client operations and run them against every
schema change. Adding a nullable field is usually compatible; removing a
field, changing its type, or tightening nullability can break clients.

## Production signals

Record operation name, schema version, status, duration, database time,
resolver errors, result size, and query cost. Redact variables that contain
credentials or personal data. Track the slowest operations by percentile,
not only the global GraphQL endpoint latency.

## Failure behavior

GraphQL can return both `data` and `errors`. Clients must handle partial data
when nullable fields fail. Define which failures are user-correctable,
retryable, or internal, and use stable error codes without exposing stack
traces.

For subscriptions, monitor connection count, delivery lag, disconnects, and
backpressure. Do not introduce subscriptions until reconnect, authorization,
and missed-event behavior are specified.

## Testing query cost and batching

Keep a representative operation corpus with shallow, deeply nested, wide,
aliased, and paginated queries. Assert that rejected operations fail before
database work begins. For accepted operations, record a query budget and fail
tests when a resolver introduces an unexpected N+1 query.

Authorization tests must exercise the same operation through direct and nested
paths. A user who cannot read a draft directly must not read it through an
author, tag, search result, or connection.

## Schema change workflow

1. Build the schema and run breaking-change checks.
2. Run the saved operation registry.
3. Measure deprecated-field usage.
4. Deploy additive changes.
5. Migrate clients.
6. Remove only after the usage window and rollback plan are complete.

## Practice

Create a failing operation for each of: invalid variable, unauthorized
object, partial nullable failure, N+1 relation, excessive depth, excessive
page size, and a retried mutation. Add the metric and operator action that
should accompany each failure.
