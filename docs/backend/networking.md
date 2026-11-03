# DNS, networking, and processes

Backend requests are chains of processes connected by networks. A useful
mental model is:

```text
hostname → DNS → IP → TCP/TLS connection → HTTP request → application
```

## DNS

DNS maps names to records such as addresses and aliases. Results are cached
according to their TTL, so changing a record is not an instant switch. A
production dependency should have a clear fallback or a documented failure
mode when name resolution is unavailable.

## Connections

TCP provides ordered, reliable bytes; TLS encrypts and authenticates the
connection; HTTP defines the request semantics. A connection can fail during
DNS lookup, connect, TLS negotiation, request upload, response headers, or
response body. Set timeouts that cover each phase instead of one unlimited
timeout.

Connection pools improve latency but consume finite database and service
capacity. Size pools with the whole deployment in mind: application processes
times pool size must remain below the database connection limit.

## Processes and signals

A web process should be disposable. Keep durable state in managed services,
handle termination signals, stop accepting new work, and finish or safely
requeue in-flight work. Never rely on process memory for data that must
survive a restart.

## Practice

Trace a request with `dig`, `curl -v`, and application logs. Write down which
step fails when the hostname resolves but the service still cannot be reached.
