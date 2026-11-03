# Project: notification service

Build a service that sends email or webhook notifications from durable
application events.

## Required behavior

1. Store the notification intent in the same transaction as the domain
   change.
2. Publish or enqueue it after commit.
3. Retry transient provider failures with bounded backoff.
4. Deduplicate using an idempotency key.
5. Record attempts, response category, and final status.

## Failure exercises

- The provider times out after accepting the request.
- The worker crashes after sending but before recording success.
- A message is malformed and always fails.
- The queue grows faster than workers can drain it.

Document which outcomes may duplicate and which business effects must never
duplicate.
