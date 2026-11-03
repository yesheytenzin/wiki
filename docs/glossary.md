# Glossary

| Term | Meaning |
| --- | --- |
| **Idempotency** | Repeating an operation produces the same intended effect. |
| **Invariant** | A rule that must remain true for valid system state. |
| **Latency** | Time taken by an operation, usually reported by percentile. |
| **N+1 query** | One query loads a collection, then one extra query runs per item. |
| **Outbox** | Durable record of an event written in the same transaction as its change. |
| **Race condition** | Result depends on timing between concurrent operations. |
| **Saturation** | A resource is near a capacity limit and work queues up. |
| **Serialization** | Converting data to a transport or storage representation. |
| **SLO** | A target for a service indicator, such as successful request rate. |
| **Transaction** | A group of database operations committed or rolled back together. |
| **TTL** | Time-to-live before cached or temporary data expires. |
| **Backpressure** | A mechanism that slows producers when consumers cannot keep up. |
| **Data loader** | A batching mechanism that combines related field loads to avoid N+1 queries. |
| **GraphQL schema** | The typed contract describing available operations, fields, arguments, and nullability. |
| **Resolver** | Code that computes a GraphQL field value for a particular request. |
| **Persisted query** | A registered operation identified by a stable key instead of arbitrary query text. |
