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

## Data structures

- **Strings:** values, counters, and serialized payloads.
- **Hashes:** fields belonging to one logical object.
- **Lists/streams:** ordered work or append-only records.
- **Sets:** membership and deduplication.
- **Sorted sets:** ranking and scheduled work.

Choose a structure that matches the operation. Do not store a large object as
one serialized string when callers need independent atomic field updates.

## Expiration and eviction

TTL is a correctness decision, not just memory management. A session, lock,
rate-limit window, and cache have different expiration requirements. Redis
eviction policy can remove keys earlier when memory is full, so durable
workflows must not depend on an evictable key as their only source of truth.

## Practice

Implement a token bucket with atomic operations, simulate two workers claiming
the same job, and document what happens if Redis disappears between two
commands. Add metrics for hit rate, memory, evictions, latency, and rejected
operations.

## Atomicity and races

Commands such as increment, set-if-absent, and expiration have precise atomic
semantics. A sequence of separate commands may not:

```text
worker A: read counter = 4
worker B: read counter = 4
worker A: write 5
worker B: write 5
```

The intended result of six increments is lost. Use an atomic command, a
server-side script, or a database transaction whose isolation protects the
invariant. Keep scripts bounded and version their assumptions.

## Locks are leases

A distributed lock is usually a time-limited lease, not permanent ownership.
The owner may pause longer than the TTL and another worker may acquire it.
Use fencing tokens or a durable compare-and-check when stale owners could
corrupt state. Prefer database constraints and idempotent work over locks
where possible.

## Cache stampedes

When a popular key expires, many requests can regenerate it simultaneously.
Use request coalescing, jittered expiration, stale-while-revalidate, or a
short lease. Set a fallback for cache outage and watch database load: a cache
that fails open can turn a small Redis incident into a database outage.
