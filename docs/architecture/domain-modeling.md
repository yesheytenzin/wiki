# Domain modeling and boundaries

A domain model names the rules that must remain true, the operations that
change state, and the ownership of each decision. It does not need to be a
large class hierarchy.

## Find the boundary

For an operation, write:

- the actor and authorization rule;
- the current state required;
- the valid transition;
- the invariant after success;
- the side effects;
- the retry and failure behavior.

Example: “cancel order” may be allowed only while pending, may release a
reservation, may publish an event, and must not send two cancellation emails.
Those are separate rules even if one controller action starts the process.

## Layering

- **Transport:** HTTP or GraphQL parsing, authentication context, response.
- **Application:** use-case orchestration and transaction boundary.
- **Domain:** business rules and state transitions.
- **Persistence:** queries, constraints, and durable representation.
- **Integration:** queues and external services with timeout/retry policy.

Layers are useful when they clarify ownership. Do not create a layer merely to
move code elsewhere; every collaborator should have a reason to exist and a
testable contract.

## State and invariants

Represent states explicitly when transitions have different permissions,
side effects, or recovery. A boolean such as `paid = true` often hides
intermediate states like `payment_pending`, `payment_failed`, and
`refunded`.

For each transition, define:

```text
current state + command + actor → next state + events + errors
```

Reject invalid transitions at the application boundary and protect important
ones with a database check or conditional update. An event should describe a
committed fact, not an intention that may later roll back.

## Aggregates and ownership

An aggregate is a consistency boundary: changes inside it obey one transaction
and its public operations protect its invariants. It is not necessarily one
table or one Ruby class. Do not load and lock an entire object graph when a
small conditional update can protect the rule.

When two domains need each other's data, prefer an explicit identifier or
event over hidden bidirectional coupling. Define which system owns correction
when their views diverge.

## Practice

Model an order aggregate with payment and shipment. List commands, states,
invariants, events, authorization rules, and failure recovery. Then identify
which rules require a database constraint and which can remain domain policy.
