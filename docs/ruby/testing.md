# Testing with RSpec

Test behavior at the narrowest boundary that proves the contract:

- Unit tests for deterministic domain rules.
- Request tests for HTTP status, authentication, and serialized data.
- Integration tests for database and job boundaries.

```ruby
RSpec.describe OrderTotal do
  it "sums price times quantity" do
    lines = [{ price: 12, quantity: 2 }, { price: 5, quantity: 1 }]

    expect(described_class.new(lines).call).to eq(29)
  end
end
```

Good tests make invalid states difficult to introduce. Test the invariant, not
private implementation details. Use factories sparingly: explicit inputs make
the important setup visible.

## Test design

Use the smallest test that proves the behavior:

| Test | Proves |
| --- | --- |
| Unit | A deterministic rule for known inputs |
| Request | Authentication, status, response contract, and persistence |
| Integration | Real boundaries such as a database or queue |
| System | A user-visible flow across the deployed stack |

The test pyramid is a cost heuristic, not a ban on integration tests. A
database constraint or transaction cannot be honestly tested with a pure unit
test.

## Failure cases

For each operation, cover:

- valid input and the expected result;
- missing and malformed input;
- unauthorized and forbidden access;
- duplicate or concurrent requests;
- dependency timeout and permanent failure;
- rollback behavior after a partial operation.

Assert stable public behavior: status, error code, state transition, and
side-effect contract. Avoid asserting incidental SQL order or private method
names.

## Test doubles

Use a stub to control a return value, a spy to observe a call, and a fake to
provide a working in-memory implementation. A fake is valuable only when it
obeys the same contract as production. Do not mock every internal method;
excessive mocks make refactoring harder without proving user behavior.

## Expert practice

Run tests in random order and in parallel when possible. Make time, randomness,
network calls, and job execution injectable. Add a regression test for every
production bug before changing the implementation.
