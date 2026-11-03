# Pagination, rate limits, and scheduling

These patterns appear repeatedly in backend systems.

## Keyset pagination

Order by a stable, unique pair such as `(created_at, id)`. Return the last
pair as a cursor and request rows after it. This avoids scanning and discarding
every earlier page:

```sql
SELECT id, created_at, title
FROM posts
WHERE (created_at, id) < (:created_at, :id)
ORDER BY created_at DESC, id DESC
LIMIT 25;
```

Create an index matching the ordering and filter.

## Token bucket rate limiting

A bucket holds tokens, refills at a fixed rate, and spends one token per
request. It allows controlled bursts while enforcing a long-term average.
Decide whether limits are per user, IP, token, or endpoint, and return
`429 Too Many Requests` with retry guidance.

## Scheduling

A priority queue can select the next job by due time. Make claiming atomic so
two workers cannot process the same item, and include a lease or visibility
timeout so abandoned work becomes available again.

## Cursor correctness

A cursor is a position in an ordering, not an offset. Encode all ordering
columns, sign or protect the cursor if clients must not modify it, and reject
unknown or expired cursor formats. Test inserts and deletes between page
requests; no item should unexpectedly repeat or disappear within the agreed
consistency model.

## Rate-limit correctness

Define whether the limit is strict, approximate, or eventually consistent
across regions. Clock skew and concurrent increments can change the result.
Prefer a server-side atomic operation and return remaining quota and retry
information only when doing so does not reveal sensitive policy.

## Scheduling correctness

Separate “ready to run” from “claimed by a worker.” A lease must expire, but
the work must be idempotent because the lease can expire while the original
worker is still running. Record attempt count and next-attempt time so
operators can distinguish a slow job from a poison message.
