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

Common Rails performance failures include N+1 queries, unbounded serialization,
large synchronous jobs, and missing database indexes. Fix the boundary that
causes the cost rather than adding a cache to hide it.
