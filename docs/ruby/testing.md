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
