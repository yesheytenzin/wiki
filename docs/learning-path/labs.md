# Hands-on labs

These labs are small enough to run locally and focused enough to inspect with
a debugger. Create a directory for each lab, write a failing test first, and
keep the final implementation available for review.

## Lab 1: Ruby order total

Create `order_total.rb`:

```ruby
class OrderTotal
  def initialize(lines)
    @lines = lines
  end

  def call
    @lines.sum do |line|
      price = line.fetch(:price_cents)
      quantity = line.fetch(:quantity)
      raise ArgumentError, "quantity must be positive" unless quantity.positive?

      price * quantity
    end
  end
end

order = OrderTotal.new([
  { price_cents: 1_200, quantity: 2 },
  { price_cents: 500, quantity: 1 }
])
puts order.call # 2900
```

### Exercises

1. Reject negative prices.
2. Add a percentage tax without using floating-point money.
3. Write tests for empty lines, missing keys, zero quantity, and large values.
4. Explain which checks belong in the database if lines are persisted.

## Lab 2: SQL invariant

Create two users and try to insert the same email from two sessions. Add:

```sql
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE UNIQUE INDEX users_email_unique ON users (lower(email));
```

The application validation improves the error message, but the unique index
protects the invariant under concurrent writes.

### Exercises

1. Explain why a pre-check followed by an insert races.
2. Decide whether email case should be significant.
3. Handle the unique-violation error as a client-visible conflict.
4. Use `EXPLAIN` to inspect a lookup by normalized email.

## Lab 3: Idempotent job

Model a payment operation with a unique key:

```sql
CREATE TABLE payment_operations (
  id BIGSERIAL PRIMARY KEY,
  idempotency_key TEXT NOT NULL UNIQUE,
  status TEXT NOT NULL CHECK (status IN ('pending', 'succeeded', 'failed')),
  provider_reference TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

The worker claims or creates the operation, calls the provider with the same
key, and records the result. A retry must return the existing result rather
than creating a second charge.

### Exercises

1. Kill the worker after the provider accepts the charge.
2. Retry after a timeout with an unknown provider outcome.
3. Define what an operator does with a permanently pending operation.
4. Add structured fields for attempt, duration, and provider response class.

## Lab 4: GraphQL query cost

Design this operation:

```graphql
query Articles($first: Int!, $after: String) {
  articles(first: $first, after: $after) {
    nodes { id title author { id name } tags { id name } }
    pageInfo { hasNextPage endCursor }
  }
}
```

Set a maximum page size, estimate the cost of `author` and `tags`, and verify
that nested loading is batched. Compare SQL count before and after the fix.

### Exercises

1. Add a depth limit and a cost limit.
2. Attempt an alias-based repeated query.
3. Verify that a private draft cannot be reached through `author`.
4. Add an operation test that fails if SQL exceeds the budget.

## Lab 5: Failure matrix

For one endpoint, inject:

- invalid input;
- unauthorized access;
- database timeout;
- provider timeout after remote success;
- queue backlog;
- cache outage;
- process termination during a transaction.

For each failure, record HTTP/GraphQL result, durable state, retry behavior,
metric, alert, and recovery action.
