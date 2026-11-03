# PostgreSQL and data modeling

Start with invariants and access patterns, then choose tables and indexes.
Use foreign keys, `NOT NULL`, checks, and unique constraints to protect data
even when multiple application versions or workers write concurrently.

## Transaction isolation

A transaction groups changes, while isolation controls what concurrent
transactions can observe. Keep transactions short, lock only what must be
serialized, and retry serialization failures when the operation is safe to
repeat.

## Query checklist

1. Measure the actual query with `EXPLAIN (ANALYZE, BUFFERS)`.
2. Check selectivity and whether the index matches the predicate.
3. Avoid selecting columns the caller does not need.
4. Paginate with a stable ordering; keyset pagination is often better than a
   large `OFFSET`.
5. Treat migrations as production code: deploy additive changes before code
   that depends on them.
