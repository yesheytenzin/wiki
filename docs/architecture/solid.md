# SOLID design principles

SOLID is a set of design heuristics for keeping software changeable:

1. **Single Responsibility Principle**
2. **Open/Closed Principle**
3. **Liskov Substitution Principle**
4. **Interface Segregation Principle**
5. **Dependency Inversion Principle**

These are not rules for creating more classes. They help identify where a
change belongs, which behavior must remain stable, and which dependency should
be replaceable. Apply them when a system has real change pressure; do not
split every five-line method into an abstraction.

## Before SOLID: coupling and cohesion

SOLID becomes easier to apply when two measures are clear:

- **Cohesion:** how strongly the responsibilities inside one unit belong
  together. High cohesion means a class has a focused purpose.
- **Coupling:** how much one unit knows about or depends on another. Low
  coupling means a change can remain local.

The goal is not minimum coupling at any cost. A useful design has cohesive
units and explicit coupling at boundaries. A database model may reasonably
couple itself to persistence, while a pricing policy should not know how an
email provider works.

## How the principles fit together

The principles reinforce one another:

```text
cohesive policy
    ↓
small interface → substitutable adapter
    ↓
dependency injected at a boundary
    ↓
new variation added without changing stable policy
```

SRP identifies responsibilities. OCP gives likely variation an extension
point. LSP checks whether implementations honor the point. ISP keeps the
point small. DIP makes the high-level policy depend on the point rather than
an infrastructure detail.

## 1. Single Responsibility Principle

A class should have one cohesive reason to change. “One responsibility” means
one area of policy, not one method.

### Coupled example

```ruby
class Invoice
  def total
    lines.sum { |line| line.price * line.quantity }
  end

  def save_to_database
    # persistence policy
  end

  def send_email
    # delivery policy
  end
end
```

Pricing, persistence, and delivery change for different reasons. A clearer
boundary keeps the domain rule independent:

```ruby
class InvoiceTotal
  def self.call(lines)
    lines.sum { |line| line.fetch(:price) * line.fetch(:quantity) }
  end
end

class InvoiceMailer
  def self.send(invoice)
    # delivery policy
  end
end
```

### Rails application

Controllers should coordinate authentication, input, and response rendering.
They should not own pricing, payment, email, and persistence policies at once.
An operation such as `Orders::Create` can own the application transaction,
while models and database constraints protect the underlying invariants.

### Warning signs

- a class has unrelated reasons in its commit history;
- a test needs a database, HTTP client, clock, and mailer to test one rule;
- a change to formatting risks changing persistence behavior.

## 2. Open/Closed Principle

Software should be open for new behavior and closed to risky modification of
stable behavior. This does not mean “never edit existing code.” It means
choose an extension seam when new variants are expected.

### Strategy objects

```ruby
class ShippingCost
  def initialize(strategy)
    @strategy = strategy
  end

  def call(order)
    @strategy.call(order)
  end
end

class FreeShipping
  def call(_order)
    0
  end
end

class DistanceShipping
  def call(order)
    order.fetch(:kilometers) * 10
  end
end
```

Adding a shipping policy does not require changing a conditional containing
every existing policy. Keep the interface small and test each policy.

### When not to use it

If there are only two stable cases, a clear `case` expression may be better
than a hierarchy. Abstraction has a maintenance cost; wait until variation is
real or a test seam is needed.

## 3. Liskov Substitution Principle

If code expects an abstraction, every implementation must honor its contract.
A subtype must not require stronger inputs, return weaker guarantees, or
introduce surprising side effects.

```ruby
class PaymentGateway
  def charge(amount_cents)
    raise NotImplementedError
  end
end

class FakeGateway < PaymentGateway
  def charge(amount_cents)
    raise ArgumentError if amount_cents <= 0

    { status: :approved, amount_cents: amount_cents }
  end
end
```

If a production gateway returns an approval object but a fake returns `true`,
tests are not substituting the real contract. Define the result and failure
semantics first, then make every adapter obey them.

### Common violation

An implementation that raises `NotImplementedError` for a method promised by
the interface is not a valid substitute. Model capabilities separately rather
than forcing unrelated implementations into one base class.

## 4. Interface Segregation Principle

Clients should not depend on methods they do not use. Ruby uses duck typing,
so an interface can be an implicit set of messages rather than a formal
interface declaration.

### Too broad

```ruby
class UserStore
  def find; end
  def save; end
  def delete; end
  def export_csv; end
  def notify; end
end
```

Separate the needs of each caller:

```ruby
class UserReader
  def initialize(store)
    @store = store
  end

  def find(id)
    @store.find(id)
  end
end

class UserWriter
  def initialize(store)
    @store = store
  end

  def save(user)
    @store.save(user)
  end
end
```

Small interfaces make fakes simpler and prevent a read-only use case from
gaining write permissions accidentally.

## 5. Dependency Inversion Principle

High-level policy should not depend directly on low-level details. Both should
depend on a stable abstraction owned by the policy.

### Inject collaborators

```ruby
class PasswordReset
  def initialize(user_store:, token_generator:, clock:)
    @user_store = user_store
    @token_generator = token_generator
    @clock = clock
  end

  def call(email)
    user = @user_store.find_by_email(email)
    return unless user

    token = @token_generator.call
    user.issue_reset_token!(token, expires_at: @clock.call + 3600)
  end
end
```

The use case owns the policy. It does not construct a global clock, random
generator, or database client inside the method. Tests can provide deterministic
collaborators, while production wires real adapters at the application
boundary.

Dependency injection is not limited to containers. Constructor arguments,
blocks, and small adapter objects are often enough.

## Putting SOLID together

Consider a notification use case:

```text
GraphQL resolver / controller
        ↓
SendNotification operation
        ↓
Notification policy + repository interface
        ↓
database repository, queue adapter, provider adapter
```

- The resolver owns transport concerns.
- The operation owns orchestration and transaction boundaries.
- The policy owns notification rules.
- Adapters own database, queue, and provider details.
- Tests replace adapters without changing the policy.

The boundaries are useful only if their contracts are explicit: inputs,
outputs, errors, retries, authorization, and transaction behavior.

## Refactoring procedure

1. Add characterization tests around the current behavior.
2. Identify the change that is difficult or risky.
3. Extract the smallest cohesive policy or collaborator.
4. Inject the dependency at the boundary.
5. Keep the public behavior unchanged.
6. Remove duplication only after the new seam is proven.

Do not refactor toward SOLID by intuition alone. Use a failing test, a
repeated variation, a difficult dependency, or a concrete ownership problem
as the trigger.

## Practice

Refactor a Rails `OrdersController#create` that validates an order, charges a
payment, saves the order, sends an email, and renders JSON:

- identify each reason to change;
- define the transaction boundary;
- create a payment adapter contract;
- make retries safe;
- test declined payment, database failure, and email failure separately;
- explain which invariant belongs in the database.

## Complete study exercise

Create a small order system in plain Ruby before using Rails:

1. Define an order state machine: `pending → paid → shipped` and
   `pending → cancelled`.
2. Add a payment gateway and a fake gateway with the same contract.
3. Add a notification adapter that can fail transiently.
4. Make the payment operation idempotent.
5. Add a repository boundary and an in-memory implementation for tests.
6. Add a Rails or GraphQL transport adapter.
7. Record the design decisions and the reasons not to abstract stable code.

For each step, write a test before refactoring. Compare the initial design and
the final design by the number of unrelated files changed for a new payment
provider or notification channel.

## Related video study

These links open focused YouTube searches so you can choose a current talk
with the language and depth you prefer:

- [SOLID principles in Ruby](https://www.youtube.com/results?search_query=SOLID+principles+Ruby)
- [SOLID principles with Rails examples](https://www.youtube.com/results?search_query=SOLID+principles+Ruby+on+Rails)
- [Dependency inversion and dependency injection](https://www.youtube.com/results?search_query=dependency+inversion+dependency+injection+Ruby)
- [Refactoring Rails service objects](https://www.youtube.com/results?search_query=refactoring+Rails+service+objects+SOLID)
- [Cohesion and coupling explained](https://www.youtube.com/results?search_query=cohesion+coupling+software+design)

When studying a video, do not copy the class structure immediately. Identify
the change pressure the speaker is addressing, then reproduce the example in
your own code and test the trade-off.

## Related wiki pages

- [Ruby internals and concurrency](../ruby/internals-concurrency.md)
- [Domain modeling and boundaries](domain-modeling.md)
- [Consistency and concurrency](consistency.md)
- [GraphQL schemas and resolvers](../graphql/schemas-resolvers.md)
- [Testing with RSpec](../ruby/testing.md)
- [API design](api-design.md)

## Self-check

- What is the actual reason this class changes?
- Which behavior is varying, and where should that variation live?
- Can every implementation honor the same preconditions and postconditions?
- Is a client forced to depend on methods it cannot use safely?
- Which high-level policy is coupled to a low-level detail?
- What contract would let a test replace the dependency honestly?
