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
