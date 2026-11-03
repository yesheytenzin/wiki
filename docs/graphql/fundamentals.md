# GraphQL fundamentals

GraphQL is a typed query language and execution model. The client asks for a
selection set; the server validates that selection against a schema and
resolves the requested fields.

```graphql
query OrderDetails($id: ID!) {
  order(id: $id) {
    id
    status
    lineItems {
      quantity
      product { name }
    }
  }
}
```

## Core operations

- **Query:** read data.
- **Mutation:** perform a state change and return its result.
- **Subscription:** receive pushed updates; add only when the product truly
  needs live delivery.

Fields can be nullable or non-null. A non-null child failure can null its
parent selection, so nullability is part of the client contract, not a
decoration.

## GraphQL versus REST

GraphQL can reduce client round trips and let clients choose fields. It also
creates new responsibilities: query cost control, resolver performance,
schema governance, and error semantics. REST's resource and HTTP semantics
remain useful for file downloads, webhooks, caching at the edge, and simple
public endpoints. Choose per boundary rather than treating one as universally
superior.

## Rails boundary

In a Rails application, keep GraphQL types and resolvers as transport
adapters. Authentication, authorization, domain operations, transactions,
and durable invariants should remain explicit in application/domain layers.
A resolver should coordinate, not become a second business-logic framework.

## Execution and errors

GraphQL validates the document before resolving fields. Execution then walks
the selection set and may resolve sibling fields in parallel depending on the
server. Resolver code must not assume a particular order unless the schema
contract makes it explicit.

Errors are associated with response paths. A nullable field can become `null`
while other data succeeds; a non-null failure can propagate null upward. Define
stable error codes so clients can distinguish invalid input, unauthorized
access, unavailable dependencies, and internal failures.

## Schema governance

Treat the schema like source code consumed by other teams. Review field names,
nullability, pagination, authorization, cost, and deprecation before merging.
Track operation usage so removal is evidence-based.

Do not expose every internal model or allow arbitrary unbounded nesting. The
schema is a product boundary with security and capacity consequences.

## Practice

Design a `Post` schema with authors, comments, and tags. Mark fields nullable
or non-null with reasons, define pagination, write one authorized mutation,
and document how a partial resolver failure appears to a client.
