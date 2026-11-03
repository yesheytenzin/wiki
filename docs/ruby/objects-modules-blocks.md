# Objects, modules, and blocks

Modules are useful for shared behavior and namespaces, but they should not
become an untracked dependency container. Prefer composition when a class
needs a collaborator with a distinct responsibility.

Blocks make APIs expressive:

```ruby
def measure
  started_at = Process.clock_gettime(Process::CLOCK_MONOTONIC)
  result = yield
  [result, Process.clock_gettime(Process::CLOCK_MONOTONIC) - started_at]
end
```

`Proc` and `lambda` capture callable behavior. Keep the boundary explicit:
accept a callable when callers genuinely need to supply policy, rather than
using metaprogramming to hide control flow.

## Backend connection

Rails callbacks, middleware, and enumerators all rely on these same Ruby
mechanisms. Understanding blocks makes it easier to reason about transaction
blocks and whether code runs before, during, or after a request.

## Composition over inheritance

Inheritance shares implementation and creates a strong coupling to the
parent's assumptions. Composition makes the dependency visible:

```ruby
class InvoicePresenter
  def initialize(invoice, currency:)
    @invoice = invoice
    @currency = currency
  end

  def summary
    "#{@currency} #{@invoice.total}"
  end
end
```

Use inheritance when the subtype genuinely preserves the parent's complete
contract. Use composition when behavior is a replaceable policy, adapter, or
collaborator.

## Modules and mixins

`include` adds instance methods; `extend` adds methods to one object;
`prepend` changes method lookup order. Mixins are appropriate for a coherent
capability with a stable contract. They become risky when they depend on
hidden instance variables, callbacks, or methods the host may not have.

Prefer a module that says what it needs:

```ruby
module Auditable
  def audit(event)
    audit_log.append(event: event, actor_id: actor_id)
  end
end
```

Document or test the required `audit_log` and `actor_id` interface.

## Enumerators and lazy work

`Enumerator` lets a producer yield values without constructing the entire
collection:

```ruby
def pages(client)
  Enumerator.new do |yielder|
    page = 1
    loop do
      rows = client.fetch(page)
      break if rows.empty?

      rows.each { |row| yielder << row }
      page += 1
    end
  end
end
```

Use lazy iteration for large data, but define behavior for network failures,
partial consumption, and repeated enumeration.

## Practice

- Replace a subclass hierarchy with composition where the behavior is a
  policy.
- Write a module contract test for a host object.
- Build a lazy importer that processes records in bounded batches.
