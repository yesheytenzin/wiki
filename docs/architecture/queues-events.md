# Queues and events

A queue asks a worker to perform work. An event announces that something
happened. The distinction matters: a command usually has one intended
consumer, while an event may have several independent consumers.

## Reliable publication

If a database transaction commits but event publication fails, the system is
inconsistent. The transactional outbox pattern stores the event in the same
database transaction, then a worker publishes pending rows and marks them
sent.

Consumers must still be idempotent because publication and delivery can be
duplicated. Store a processed-event identifier or use a domain-level unique
constraint where appropriate.

## Queue design questions

- What is the maximum acceptable delay?
- Which failures are retryable?
- How many attempts are safe?
- What happens to poison messages?
- Can work be reordered?
- How do operators replay or repair a message?

Keep payloads small and version them. Prefer IDs plus a lookup when current
state is required; include a snapshot when historical meaning must not change.
