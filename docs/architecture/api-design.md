# API design

An API is a contract between independently changing clients and servers. Start
with a use case and document the contract before implementation.

## Contract checklist

- resource names and nesting;
- authentication and authorization;
- request fields, types, defaults, and limits;
- success and error status codes;
- pagination and ordering;
- idempotency behavior;
- rate limits and retry guidance;
- compatibility and deprecation policy.

Prefer additive evolution. If a change cannot be understood by an older
client, version or introduce a new endpoint rather than silently changing the
old contract.

## Pagination

Offset pagination is easy to understand but becomes expensive and unstable
when rows are inserted. Cursor or keyset pagination uses a stable ordered
column and is generally better for large, changing collections.

Always return enough metadata for a client to request the next page, and set a
maximum page size on the server.

## Resource and action design

Use nouns for resources and reserve action endpoints for domain operations
that are not ordinary CRUD:

```text
POST /orders
GET  /orders/:id
POST /orders/:id/cancellation
```

The cancellation endpoint makes a state transition explicit. It can define
authorization, idempotency, and failure behavior without pretending that
cancellation is a generic update to an arbitrary `status` field.

## Compatibility

Classify changes as additive, behavioral, or breaking. Adding an optional
response field is usually additive. Changing a default, enum meaning,
ordering, error code, or nullability may break a client even when the URL is
unchanged.

Use contract tests or an operation registry to catch changes before release.
Deprecate with a timeline and usage measurement; do not remove a field because
it appears unused in one client.

## Operational contract

Document timeout expectations, retry safety, rate limits, maximum body and
page sizes, request IDs, and error codes. A client cannot safely retry an API
that has not defined whether a timed-out mutation may have succeeded.

## Practice

Design an API for creating and cancelling orders. Write the OpenAPI-like
contract in prose, include all error cases, then implement both a REST and a
GraphQL version. Compare caching, pagination, client ergonomics, and failure
semantics.

## Worked contract

```text
POST /orders
Request: { "line_items": [{ "product_id": "p1", "quantity": 2 }] }
201:     { "id": "o1", "status": "pending" }
401:     unauthenticated
422:     invalid quantity or unavailable product
409:     idempotency key already represents a different request
```

The `409` case is distinct from invalid syntax: the request is well-formed,
but conflicts with existing operation state. A timeout does not tell the
client which response occurred, so the client retries with the same
idempotency key and receives the stored result.
