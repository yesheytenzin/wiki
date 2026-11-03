# Background jobs

Move slow or failure-prone work out of the request when the caller does not
need the result immediately. A job must assume it can run zero, one, or many
times.

```ruby
class SendReceiptJob < ApplicationJob
  retry_on Net::ReadTimeout, wait: :polynomially_longer, attempts: 5

  def perform(order_id)
    order = Order.find(order_id)
    Receipts::Send.call(order)
  end
end
```

Design jobs around:

- **Idempotency:** repeated execution has one intended effect.
- **Retries:** transient failures are retryable; permanent failures are not.
- **Observability:** log job ID, entity ID, attempt, duration, and outcome.
- **Boundaries:** do not hold database transactions open while calling remote
  services.

## Job lifecycle

```text
enqueue → reserve → perform → acknowledge
              ↓
          timeout/retry → dead letter
```

Keep the payload small and stable. Prefer an ID plus a lookup when current
state is required. Include a snapshot when the job must preserve historical
meaning.

Use a dead-letter or failed-job workflow that a person can inspect and repair.
Make the repair action safe to repeat and record who performed it.

## Practice

Implement a job that sends a receipt. Test a successful delivery, a transient
timeout, a permanent provider error, and a worker crash after delivery. State
which outcomes can duplicate and how the application prevents duplicate
charges or messages.
