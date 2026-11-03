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
