# GraphQL schemas and resolvers

The schema is a public contract. Design it around client use cases and domain
capabilities, not around every database column.

```graphql
type Order {
  id: ID!
  status: OrderStatus!
  totalCents: Int!
  lineItems(first: Int!, after: String): LineItemConnection!
}

type Query {
  order(id: ID!): Order
}

type Mutation {
  cancelOrder(input: CancelOrderInput!): CancelOrderPayload!
}
```

## Schema design rules

- Use domain names, not persistence names, when they differ.
- Make nullability intentional and document why a value may be absent.
- Use input objects for mutations so validation can evolve additively.
- Return payloads with the changed object and structured errors.
- Prefer connections for collections so pagination is explicit.
- Deprecate fields before removal and measure their use.

## Resolver responsibilities

A resolver should:

1. read the authenticated actor from context;
2. validate input through the application boundary;
3. authorize the operation and returned object;
4. load data through a bounded query path;
5. return a value matching the schema.

Do not authorize only at the root query. A nested field can expose sensitive
data even when its parent is allowed. Apply object and field-level policy where
the data becomes visible.

## Mutations

A mutation should have a clear transaction boundary and idempotency strategy.
For a retried mutation, accept a client-provided idempotency key and persist
the result or a domain-level unique operation record.
