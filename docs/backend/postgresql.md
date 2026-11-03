# PostgreSQL and data modeling

Start with invariants and access patterns, then choose tables and indexes.
Use foreign keys, `NOT NULL`, checks, and unique constraints to protect data
even when multiple application versions or workers write concurrently.

## Modeling workflow

1. Name the entities and their ownership.
2. Write the invariants in plain language.
3. Choose cardinality: one-to-one, one-to-many, or many-to-many.
4. Add constraints that make invalid states impossible.
5. List the read and write paths.
6. Add indexes for measured or clearly critical access paths.

Normalize data by default. Denormalize only when the read benefit is measured
and there is a clear source of truth and refresh strategy.

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

## Safe migration sequence

For a renamed or removed column, use an expand-and-contract migration:

1. Add the new column or table.
2. Deploy code that writes both representations.
3. Backfill in bounded batches.
4. Switch reads to the new representation.
5. Stop writing the old representation.
6. Remove it in a later release.

This keeps old and new application versions compatible during a rolling deploy.

## Practice

Design the schema for a URL shortener. Protect the short code with a unique
index, decide whether deleted URLs remain reserved, and explain how clicks are
recorded without slowing redirects.

## Indexes from first principles

An index is an additional ordered or hashed structure that lets the database
find rows without scanning the whole table. It speeds selected reads by
adding storage, write work, vacuum work, and planner choices.

An index helps only when its leading columns match the filter or ordering and
the selected rows are sufficiently selective. A composite index on
`(account_id, created_at, id)` can support an account-scoped keyset query;
reversing the columns may not.

Use `EXPLAIN (ANALYZE, BUFFERS)` to compare estimated and actual rows. Large
differences indicate stale statistics, skewed data, or a query the planner
does not understand. Never run `ANALYZE` casually in production without
understanding its locking and workload effects.

## Isolation and anomalies

Concurrency anomalies include dirty reads, non-repeatable reads, phantom
rows, lost updates, and write skew. PostgreSQL's default isolation prevents
some anomalies but does not make arbitrary application rules safe. Express
the rule with a constraint or lock and test two concurrent transactions.

A deadlock is not necessarily a data bug: two transactions acquired locks in
opposite order. Keep transactions short, lock resources in a consistent
order, and retry only operations that are safe to repeat.

## Backups and recovery

A backup is evidence of recoverability only after a restore test. Define
recovery point and recovery time objectives, test point-in-time recovery, and
verify that application secrets, migrations, extensions, and external side
effects are handled. A restored database must not automatically resend old
notifications or webhooks.
