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

## Cost analysis

Depth counts nesting but not actual work. A field that performs a full-text
search or loads a large relation may cost more than several scalar fields.
Assign field weights, multiply them through list selections, and cap the
result. Treat client-provided limits as inputs to validation, not guarantees.

Reject expensive operations before resolving them. A timeout after partial
execution still consumes database and provider capacity.

## Authorization through nested fields

Authorization must follow the data path:

```text
viewer → organization → project → private document
```

Checking only that the viewer can access the organization is insufficient if
project membership or document visibility has a second rule. Data loaders must
carry the same authorization scope as direct loads and must not cache a result
for one actor and return it to another.

## Production hardening

Separate public and internal schemas when their trust models differ. Consider
disabling unrestricted introspection in production while retaining a safe
schema registry for trusted clients. Persist approved operations, enforce
variable limits, redact logs, and test aliases, fragments, recursive
selections, and deeply nested connections.
