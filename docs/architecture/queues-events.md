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

## Event evolution

Events are contracts between producers and consumers. Add optional fields
before relying on them, tolerate unknown fields, and version a breaking
semantic change. A consumer should not fail because a producer added metadata
it does not use.

Record event ID, type, schema version, aggregate ID, occurred-at time, and
causation or correlation ID. These fields make replay and incident
investigation possible without copying sensitive payloads into every log.

## Ordering and partitioning

Global ordering is expensive and often unnecessary. If order matters, define
the scope: per account, order, or aggregate. Partition by that key and make
the consumer reject or defer an event that arrives before its predecessor.

Do not assume queue order survives retries, multiple workers, or multiple
partitions. Put the required sequence in the data model.

## Backpressure and replay

Monitor queue depth and oldest message age, not just worker CPU. When producers
outpace consumers, bound the queue, reject optional work, or reduce production.
An unbounded queue moves failure into memory and delays.

Replay requires idempotent consumers, a time range or event selection, rate
limits, and a way to avoid sending external side effects twice. Test replay
against a copy or dry-run mode before changing production.
