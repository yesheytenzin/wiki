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

## What happens in detail

1. The web server accepts bytes and applies connection and body limits.
2. Rails middleware assigns request context, parses cookies, and handles
   cross-cutting concerns.
3. The router matches method and path to a controller action.
4. Authentication establishes an actor; authorization checks the operation.
5. Parameter handling validates shape and permitted fields.
6. Application code reads or changes domain state.
7. The database commits or rolls back the transaction.
8. The serializer builds a bounded response.
9. Logs, metrics, and traces record the outcome without secrets.

The order matters. Authorization before loading an unscoped record prevents
data exposure; committing before enqueueing an outbox event prevents a worker
from observing state that later rolls back.

## Transactions and callbacks

A controller action is not automatically a transaction. Put the transaction
around the smallest group of writes that must succeed together. Do not hold it
open while calling a remote service. Use after-commit behavior or an outbox
for effects that depend on durable state.

Callbacks can make lifecycle behavior convenient but hidden. Use them for
local, unsurprising invariants; make external calls and important workflows
explicit operations that can be tested and retried.

## Performance and failure

Measure time spent in routing, application code, SQL, serialization, and
downstream calls. A fast controller can still produce a slow request through
an N+1 query or an unbounded response. Define timeouts and handle client
disconnects so abandoned work does not consume resources indefinitely.

## Practice

Trace one request from `curl` to a committed row. Add a request ID, a
transaction failure, an authorization failure, and a slow dependency. For
each case, document status, logs, metrics, database state, and retry behavior.

## Worked Rails example

```ruby
class OrdersController < ApplicationController
  before_action :authenticate_user!

  def show
    order = current_user.orders.includes(:line_items).find(params[:id])
    render json: OrderSerializer.new(order)
  end

  def create
    order = Orders::Create.call(
      actor: current_user,
      attributes: order_params.to_h
    )
    render json: OrderSerializer.new(order), status: :created
  rescue Orders::Create::Invalid => error
    render json: { error: { code: "invalid_order", message: error.message } },
      status: :unprocessable_entity
  end

  private

  def order_params
    params.require(:order).permit(line_items: %i[product_id quantity])
  end
end
```

The controller owns transport concerns. `Orders::Create` owns application
orchestration, the model/database own durable invariants, and the serializer
owns the response shape. Request tests should verify authentication,
authorization, permitted parameters, status, response fields, and rollback.
