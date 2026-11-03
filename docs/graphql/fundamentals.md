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
