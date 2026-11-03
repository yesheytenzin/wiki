# Distributed systems

Once work crosses a process or network boundary, assume delay, duplication,
reordering, and partial failure. Design around those facts instead of treating
remote calls like local method calls.

Useful patterns include:

- timeouts and bounded retries with jitter;
- idempotency keys for client retries;
- durable queues for work that can be delayed;
- circuit breakers or load shedding for unhealthy dependencies;
- an outbox for publishing events after a database commit;
- explicit consistency expectations.

Ask what happens if each dependency is slow, unavailable, or returns success
after the caller times out. Those answers are part of the API design.
