# Complexity and data structures

Big-O describes how work or memory grows with input size. It is a useful
comparison tool, not a complete performance prediction: constants, I/O,
allocation, cache behavior, and contention also matter.

| Structure | Typical lookup | Useful for |
| --- | ---: | --- |
| Array | O(1) by index | Ordered data and scans |
| Hash | O(1) average by key | Membership and direct lookup |
| Heap | O(log n) insert/remove | Prioritized work |
| Tree index | O(log n) lookup | Ordered ranges |
| Graph | Depends on traversal | Dependencies and relationships |

For every choice, state the invariant. A heap keeps the highest-priority item
at the root; a hash table maps a key to a bucket; a queue preserves processing
order.

## Backend application

Database indexes are data structures with write and storage costs. A fast read
is not free: every insert or update may maintain several indexes. Measure the
read/write mix before adding one.
