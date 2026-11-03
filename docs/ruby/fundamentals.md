# Ruby fundamentals

Ruby is object-oriented: values have behavior, and method calls are the main
way objects collaborate. Backend code becomes easier to change when methods
have one clear responsibility and make their inputs and outputs obvious.

```ruby
class OrderTotal
  def initialize(lines)
    @lines = lines
  end

  def call
    @lines.sum { |line| line.fetch(:price) * line.fetch(:quantity) }
  end
end
```

Prefer `fetch` when missing data is a programmer or data-contract error.
Choose `dig` or explicit guards when absence is expected.

## Collections and errors

Use `map` to transform, `select` to filter, and `each` for side effects.
Raise a domain-specific error when the caller can take a useful action:

```ruby
class InsufficientFunds < StandardError; end

def withdraw(balance, amount)
  raise InsufficientFunds if amount > balance

  balance - amount
end
```

Avoid rescuing `StandardError` at low-level helpers unless you can recover or
add context. Let the boundary that reports the failure decide how to handle it.

## Practice

- Rewrite a nested loop using `Enumerable`.
- Add tests for zero, negative, and missing quantities.
- Identify which methods mutate state and which return new values.

## Core concepts to master

### Names, values, and mutation

Ruby variables hold references to objects. Assignment does not copy the
object:

```ruby
first = ["ruby"]
second = first
second << "rails"

first # => ["ruby", "rails"]
```

Use `dup` or an immutable value when a method must not share mutable state.
`freeze` can document and enforce an invariant, but it does not recursively
freeze nested objects.

### Truthiness and control flow

Only `false` and `nil` are falsey in Ruby. Empty strings and empty arrays are
truthy. This matters when translating input into business rules:

```ruby
name = params[:name]
display_name = name.nil? || name.empty? ? "Anonymous" : name
```

Use explicit predicates when an empty value has a different meaning from an
absent value.

### Equality and identity

`==` expresses value equality, `equal?` tests object identity, and `eql?` and
`hash` work together for hash keys. If a value object overrides equality, it
must preserve the hash contract: equal values must have equal hashes.

## Method design

A useful method has a small input surface, one observable result, and a clear
failure behavior. Prefer keyword arguments for options whose meaning is not
obvious:

```ruby
def retry_request(url:, attempts:, timeout:)
  # ...
end
```

Avoid boolean arguments that make call sites ambiguous. Replace
`publish(order, true)` with a named policy or keyword.

## Exceptions and boundaries

Raise exceptions for exceptional failures, not ordinary control flow. Rescue
at a boundary that can recover, translate, retry, or report. Preserve context
when translating:

```ruby
def load_user(id)
  User.find(id)
rescue ActiveRecord::RecordNotFound => error
  raise UserLookupFailed, "user #{id} was not found", cause: error
end
```

Never expose the original exception or sensitive input directly in a public
response.

## Expert checklist

- Can you state the input, output, mutation, and failure behavior of each
  public method?
- Which objects are safe to share between threads?
- Which invariants are enforced by Ruby and which must be enforced by the
  database?
- Can a caller distinguish absent, invalid, and unauthorized input?
