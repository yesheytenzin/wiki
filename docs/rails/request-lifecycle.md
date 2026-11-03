# Rails request lifecycle

A typical API request flows through:

```text
client → router → middleware → controller → domain/model → database
                                      ↓
                              response serializer
```

The router chooses an action. Middleware handles cross-cutting concerns such
as request IDs and sessions. The controller authenticates, authorizes, and
coordinates. Domain code owns business rules. The database enforces durable
invariants.

Keep controllers thin, but do not move logic into an unstructured “service
object” just to reduce line count. Name a collaborator after the business
operation it owns and give it a testable input/output contract.

## A useful controller boundary

```ruby
def create
  order = Orders::Create.call(current_user, order_params)
  render json: order, status: :created
rescue Orders::Create::Invalid => error
  render json: { error: error.message }, status: :unprocessable_entity
end
```

The application should have one consistent error format and should never
return a success-shaped response when an operation failed.
