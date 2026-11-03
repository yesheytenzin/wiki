# GraphQL security and performance

GraphQL moves query shape to the client, so the server must bound work before
execution.

## Query controls

- Maximum depth limits deeply nested selections.
- Complexity or cost analysis limits expensive combinations.
- Maximum aliases prevent repeated execution of the same field.
- Request body and variable limits protect parsing and memory.
- Timeouts and cancellation stop work that no longer has a client.
- Persisted or allowlisted queries reduce arbitrary production traffic.

Limits should be measured against real queries. A single fixed depth number is
not a substitute for cost analysis: a shallow field can still scan millions
of rows.

## N+1 queries

The classic failure is resolving a collection and issuing one database query
per child. Use batching/data loaders or explicit preloading, but verify that
the batch key and authorization scope are correct. A loader must not merge
records across tenants or actors.

Inspect SQL count, database time, object count, and response size per
operation. Set a maximum page size and require pagination for large fields.

## Authorization and abuse

Authenticate once at the request boundary, then authorize each operation and
object. Do not rely on field names being hidden: a client may inspect the
schema unless introspection is controlled for the environment.

Rate-limit by actor, credential, IP, and operation where appropriate. Avoid
putting secrets or personal data in error extensions, traces, persisted-query
registries, or query logs.
