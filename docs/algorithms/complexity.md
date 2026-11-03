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

## Amortized and space complexity

An operation can be expensive occasionally while cheap over a sequence. Dynamic
arrays append in amortized constant time even though resizing one append costs
linear time. State whether a bound is worst-case, average, or amortized.

Space includes temporary allocations, recursion depth, indexes, caches, and
network buffers. A faster algorithm that exhausts memory is not an
improvement.

## Correctness before optimization

Write the invariant and a small oracle implementation first. For pagination,
the invariant may be “each item appears once in a stable order.” For a rate
limiter, it may be “accepted requests never exceed available tokens.”
Property-based or randomized tests are useful for finding edge cases in
custom data structures.

## Practice

Compare array scanning, a Ruby hash, and a PostgreSQL index for membership
lookups. Record time, memory, update cost, and behavior under duplicate,
missing, and adversarial keys.
