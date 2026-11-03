# Distributed systems

Once work crosses a process or network boundary, assume delay, duplication,
reordering, and partial failure. Design around those facts instead of treating
remote calls like local method calls.

Useful patterns include:

- timeouts and bounded retries with jitter;
- idempotency keys for client retries;
- durable queues for work that can be delayed;
- circuit breakers or load shedding for unhealthy dependencies;
- an outbox for publishing events after a database commit;
- explicit consistency expectations.

Ask what happens if each dependency is slow, unavailable, or returns success
after the caller times out. Those answers are part of the API design.

## Delivery semantics

- **At-most-once:** a message is not retried; loss is possible.
- **At-least-once:** messages can be duplicated; consumers must be idempotent.
- **Effectively-once:** duplicate delivery is tolerated so the business effect
  occurs once through deduplication or constraints.

“Exactly once” is rarely a property of an entire distributed workflow. State
the guarantee at each boundary and design reconciliation for ambiguous
outcomes.

## Consistency choices

Strong consistency makes a recent write immediately visible but can increase
latency and reduce availability during partition. Eventual consistency improves
independence and throughput but requires stale-read UX, reconciliation, and
clear ownership.

Do not use a distributed lock as a substitute for an invariant. Enforce
durable uniqueness and valid state in the database, then use coordination to
reduce duplicate work.

## Resilience patterns

- **Bulkhead:** isolate worker pools or connection budgets.
- **Circuit breaker:** stop calling an unhealthy dependency temporarily.
- **Load shedding:** reject optional work before the entire service collapses.
- **Hedging:** duplicate a slow read only when the dependency and cost allow it.
- **Reconciliation:** periodically compare systems and repair divergence.

Every pattern adds complexity. Define the failure it prevents, the metric that
shows it is active, and the recovery path.

## Partial failure walkthrough

Suppose an order service writes `paid`, then calls a payment provider. The
process times out after the provider accepted the charge. The local system
cannot know from the timeout alone whether the charge happened.

A robust design uses a provider idempotency key, records the local operation,
retries safely or queries provider status, and reconciles ambiguous outcomes.
It does not blindly issue a second charge or tell the user that payment
definitely failed.

Apply the same reasoning to webhooks, event publication, email, and inventory.
For every remote call, record request identity, timeout behavior, retry policy,
provider result, and repair path.

## Multi-region concerns

Regions add latency, replication lag, clock differences, and split-brain
choices. Decide where writes are authoritative, what data can be stale, and
how traffic moves during a region failure. Do not claim global availability
while relying on one region's database or control plane.
