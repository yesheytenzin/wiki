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
