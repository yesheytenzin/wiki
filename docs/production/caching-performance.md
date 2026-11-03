# Caching and performance

Measure before optimizing. Establish a latency target, identify the slow
boundary, and compare a change against representative traffic.

Cache only data that can tolerate the chosen staleness. For every cache,
define:

- key ownership and namespace;
- expiration and invalidation;
- behavior on a cache miss or outage;
- protection against stampedes;
- metrics for hit rate and latency.

## Performance workflow

1. Define the user-visible target, such as p95 latency under a known load.
2. Measure request, database, serialization, and dependency time separately.
3. Reproduce with representative data and concurrency.
4. Change one boundary and compare the same measurements.
5. Keep the improvement only if the cost and complexity are justified.

## Cache patterns

- **Read-through:** application loads and stores a miss.
- **Write-through:** a write updates the source and cache together.
- **Write-behind:** cache accepts the write and persists later; use only when
  data loss is acceptable.
- **Stale-while-revalidate:** serve a bounded stale value while refreshing.

Avoid caching authorization-sensitive responses without including the correct
identity in the key. A cache key should encode every input that changes the
result.

Common Rails performance failures include N+1 queries, unbounded serialization,
large synchronous jobs, and missing database indexes. Fix the boundary that
causes the cost rather than adding a cache to hide it.
