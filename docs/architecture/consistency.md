# Consistency and concurrency

Concurrency bugs occur when two valid operations interact in an unplanned
order. Start with the invariant, then choose a database constraint, lock,
isolation level, or retry policy that protects it.

## Common tools

- **Unique constraint:** prevents duplicate identities atomically.
- **Check constraint:** rejects invalid row state.
- **Optimistic locking:** detects a changed version and asks the caller to
  retry or reconcile.
- **Pessimistic lock:** serializes a short critical section.
- **Serializable transaction:** rejects unsafe interleavings.
- **Idempotency record:** maps repeated requests to one operation result.

Locks are not a substitute for a transaction boundary. Keep critical sections
short and acquire multiple locks in a consistent order to reduce deadlocks.

## Distributed consistency

A committed local transaction cannot atomically commit a remote API call.
Use an outbox, saga, reconciliation job, or an explicit compensating action.
Document whether the user sees the operation as synchronous, eventually
consistent, or pending.

## Practice

Implement two concurrent attempts to reserve the last inventory item. First
show the race without protection, then add a constraint or lock and test the
failure path.

## Lost update example

Two requests read a balance of 100. Both subtract 30 and write 70. Each
request was locally correct, but the final balance lost one subtraction.
Options include:

- an atomic SQL update with a predicate;
- a row lock around read and write;
- optimistic version checking;
- a ledger where each debit is an append-only fact.

Choose based on contention, audit requirements, and whether callers can retry.
An atomic update that affects zero rows should become a defined conflict, not a
successful response.

## Isolation is not magic

Isolation controls visibility and conflict behavior between transactions; it
does not validate business meaning. A serializable transaction may still
commit an invalid external side effect, and a read-only query may observe a
snapshot that becomes stale before it is shown to a user.

Document the freshness requirement: read-your-writes, monotonic reads,
eventual consistency, or strong consistency. Match the user experience and
repair process to that choice.

## Practice

Implement a balance transfer with a ledger and with a mutable balance. Run
concurrent transfers, kill a worker between writes, and compare auditability,
retry safety, query cost, and recovery behavior.
