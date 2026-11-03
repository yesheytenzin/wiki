# Ruby internals and concurrency

Expert Ruby work requires knowing what the runtime is doing underneath
objects and method calls. Ruby values are objects, references point to objects,
and mutable objects can create surprising coupling between callers.

## Object model

Classes are objects too. A method lookup searches the receiver's singleton
class, its class, prepended modules, included modules, and ancestors. Prefer
ordinary composition and small interfaces before using `method_missing`,
runtime class mutation, or broad metaprogramming.

```ruby
class Pricing
  def initialize(tax:)
    @tax = tax
  end

  def total(amount)
    amount * (1 + @tax)
  end
end
```

The object owns its policy and can be tested without Rails.

## Memory and garbage collection

Allocations increase garbage-collection work. Large JSON responses, repeated
string creation, and loading whole tables can create latency spikes. Measure
with profiling tools before changing code. Streaming and batching reduce peak
memory but require explicit cleanup and failure handling.

## Threads, processes, and fibers

Threads are useful for overlapping I/O when libraries are thread-safe. Shared
mutable state needs synchronization; a Ruby instance variable is not a
transaction. Processes provide stronger isolation and can use multiple CPU
cores, at the cost of memory and inter-process coordination. Fibers provide
cooperative scheduling and require code that yields.

Use immutable values or narrow synchronization boundaries. Never assume a
global cache, connection, or client is safe to share across threads without
checking its contract.

## Practice

Measure allocations for a serializer, compare loading 10,000 rows at once with
batching, and explain which resource is saturated. Then write a race test for
two workers updating the same record.
