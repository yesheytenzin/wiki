# Project: GraphQL content API

Build a Rails GraphQL API for articles, authors, tags, and comments. The
project should expose a useful client-facing schema without turning the
database into the API.

## Milestones

1. Design types, nullability, pagination, and error payloads.
2. Add authenticated queries for published content.
3. Add an authorized mutation for drafts and comments.
4. Prevent N+1 queries with measured batching or preloading.
5. Add depth, cost, body-size, and page-size limits.
6. Add persisted operation tests and schema compatibility checks.
7. Instrument operation latency, SQL time, errors, and result size.

## Expert exercises

- Prove that a user cannot access another author's draft through a nested
  field.
- Compare the same feature in REST and GraphQL.
- Introduce a slow resolver and identify it from production-style metrics.
- Add a mutation idempotency key and test a client retry.
- Deprecate a field without breaking the operation registry.
