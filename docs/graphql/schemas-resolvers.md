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

## Nullability and evolution

Use non-null for values the server guarantees for every successful object.
Use nullable when absence is a valid result or when a partial failure should
not remove sibling data. Tightening a nullable field to non-null can break a
client that already handles `null`; treat it as a compatibility change.

Deprecate with a reason and measure operation usage. A field is not safe to
remove because the frontend repository has no references: mobile clients,
partners, saved operations, and older releases may still use it.

## Resolver and database boundaries

A resolver should not assemble SQL independently for every field. Centralize
authorization-scoped loaders and give them explicit selection and pagination
contracts. Keep transaction and domain operations below the resolver so the
same behavior can be used by REST, jobs, or internal callers.

## Practice

For a `publishArticle` mutation, specify actor, input validation, state
transition, transaction, event publication, idempotency, payload errors, and
the fields visible to the caller. Then write the same operation as a REST
endpoint and compare the ownership boundaries.
