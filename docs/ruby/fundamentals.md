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
