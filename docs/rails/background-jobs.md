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

## Retry mathematics

Backoff reduces pressure on a failing dependency. Add jitter so thousands of
workers do not retry at the same instant. Bound total attempts and elapsed
time; an operation that is still retrying after its business value expires is
not reliable.

Classify errors:

| Error | Typical action |
| --- | --- |
| Invalid input | Mark failed and alert only if unexpected |
| Authentication/configuration | Stop or quarantine; retries will not fix it |
| Rate limit | Retry after provider guidance |
| Timeout | Retry only with idempotency protection |
| Permanent missing record | Mark handled or dead-letter |

## Job observability

Measure queue age, execution duration, attempt count, success rate, retry rate,
dead-letter count, and provider latency. Include job ID, entity ID, operation
key, and attempt in structured logs. Do not log full payloads when they contain
personal or secret data.

## Deployments and jobs

Workers and web processes may run different code during a rolling deploy.
Version job payloads, keep consumers backward-compatible, and deploy producers
only after consumers understand the new shape. A schema migration must be
expand-and-contract when queued jobs can outlive a release.
