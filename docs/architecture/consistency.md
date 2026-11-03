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
