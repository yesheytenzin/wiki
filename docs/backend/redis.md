# Redis and ephemeral data

Redis is an in-memory data store commonly used for caching, counters,
distributed locks, and job queues. It is not automatically a replacement for
durable relational data.

## Choose the right ownership

| Use | Required decision |
| --- | --- |
| Cache | What is the source of truth and how stale may this be? |
| Rate limit | What is the window, key, and behavior on Redis failure? |
| Lock | What prevents expiry or ownership loss from corrupting work? |
| Queue | How are retries, visibility, and dead letters handled? |

Set an expiration on temporary keys. Namespaces should include the
application and environment so unrelated data cannot collide.

## Failure behavior

Decide whether a Redis outage should fail open, fail closed, or degrade. A
cache outage may increase database load; a lock outage may require stopping
the operation rather than running it concurrently. Document and monitor the
chosen behavior.

Do not use a cache read followed by a separate write as an atomic counter
update. Use Redis atomic commands or a transaction appropriate to the
invariant.
