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

## Suggested data model

```text
notifications
  id
  event_id          unique
  recipient_id
  channel
  status
  attempt_count
  next_attempt_at
  last_error_code
  sent_at
```

The unique event ID prevents the same domain event from creating duplicate
notification intents. A separate provider idempotency key protects against a
timeout after the provider accepted a request.

## Expert extension

Add user preferences, quiet hours, provider failover, template versioning,
delivery webhooks, and an operator replay tool. Specify which preferences are
evaluated at enqueue time versus delivery time and how a change affects
already queued notifications.
