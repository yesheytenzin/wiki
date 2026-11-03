# Algorithms for backend systems

Choose an algorithm by its invariant, complexity, memory use, and operational
behavior—not by how clever the implementation looks.

| Problem | Common approach |
| --- | --- |
| Lookup by stable key | Hash index, average O(1) |
| Ordered range query | B-tree index, O(log n) lookup |
| Large result pagination | Keyset cursor |
| Request throttling | Token bucket or leaky bucket |
| Repeated expensive computation | Cache with explicit invalidation |
| Work with priorities | Heap-backed priority queue |
| Dependency ordering | Topological sort |

For every algorithm, state input limits, worst-case complexity, and what
happens under malformed or adversarial input. Backend performance is usually
dominated by I/O and contention, so include database and network costs in the
analysis.

## Selection workflow

1. Define the operation and its correctness invariant.
2. Estimate input size, update frequency, and concurrency.
3. Choose a baseline that is easy to verify.
4. Measure with representative data.
5. Improve only the measured bottleneck.
6. Document memory, latency, failure, and maintenance costs.

## Backend examples

### Deduplication

Use a set or unique database index when the invariant is “one identifier is
processed once.” An in-memory set is not enough across processes or restarts.

### Top-K results

A heap of size `k` uses `O(n log k)` time and `O(k)` memory, useful when `k` is
small and the input is large. Sorting everything uses `O(n log n)` time and
may require much more memory.

### Dependency ordering

Topological sorting detects an ordering for acyclic dependencies. A cycle is
not a sorting failure to hide; it is a domain or configuration error that
should be reported with the cycle members.

## Practice

Implement a bounded top-K endpoint, a dependency scheduler, and a deduplicated
event consumer. For each, test empty input, duplicate input, malformed input,
large input, and a failure midway through processing.
