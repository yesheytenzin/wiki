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
