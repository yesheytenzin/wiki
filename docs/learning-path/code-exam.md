# Backend code exam

Complete this exam without copying the solution from another project. You may
use official Ruby, Rails, PostgreSQL, and GraphQL documentation. Submit code,
tests, a design note, and a failure report.

## Part 1: Ruby domain logic

Implement `Orders::Create` with:

- line validation;
- integer money;
- a tax policy injected as a collaborator;
- a payment gateway injected as a collaborator;
- a result object or explicit domain errors.

Write tests for valid input, invalid quantity, declined payment, and repeated
calls with the same operation key.

## Part 2: Database design

Design migrations for users, orders, line items, payment operations, and
outbox events. Include foreign keys, checks, unique indexes, timestamps, and
indexes matching the main read paths.

Explain why every constraint exists and how the migration remains compatible
with the previous application release.

## Part 3: API

Expose either REST or GraphQL operations to:

- create an order;
- retrieve an authorized order;
- cancel a pending order;
- list orders with stable pagination.

Document status/error behavior, authorization, idempotency, and maximum input
size. Add request or operation tests.

## Part 4: Production behavior

Move payment confirmation and receipt delivery to a job. Add retry
classification, backoff, dead-letter handling, structured logs, metrics, and
a runbook for a provider outage.

## Part 5: Review questions

Answer in writing:

1. Which invariant is strongest in the database?
2. What happens after a timeout with unknown payment outcome?
3. How does a retry avoid duplicate effects?
4. Where can an N+1 query occur?
5. What can the old release read during deployment?
6. Which metric detects queue consumers falling behind?
7. What is the first safe mitigation during overload?

## Grading rubric

| Area | Pass condition |
| --- | --- |
| Correctness | Invariants hold under invalid and concurrent input |
| Design | Responsibilities and dependencies are explicit |
| Security | Authorization, validation, secrets, and limits are addressed |
| Testing | Normal, failure, retry, and boundary cases are covered |
| Operations | Logs, metrics, deploy, rollback, and recovery are documented |
| Explanation | Trade-offs are justified, not merely named |
