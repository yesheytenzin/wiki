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

## Connection lifecycle

For an HTTPS request, the client normally:

1. resolves the hostname;
2. opens a TCP connection;
3. negotiates TLS and verifies the certificate;
4. sends an HTTP request;
5. receives headers and a body;
6. reuses or closes the connection.

Each step has different evidence and failure behavior. DNS failure is not the
same as connection refusal; a TLS hostname mismatch is not the same as an
application `503`; a response timeout is not the same as a connect timeout.
Expose these phases in client metrics when diagnosing latency.

## Proxies and load balancers

A reverse proxy terminates or forwards client connections and may add TLS,
compression, caching, routing, and request limits. Preserve the original
request ID and client information safely; never blindly trust a forwarded
address from an untrusted proxy.

Load balancing changes failure behavior. A healthy process can still be
unreachable through a stale target, and a health check can pass while the
critical database operation is broken. Use health checks that are deep enough
to protect users but cheap enough not to overload dependencies.

## Practice: failure matrix

Create a table for DNS failure, refused connection, certificate failure,
slow headers, slow body, malformed response, and process crash. For each,
write client timeout, user response, retry policy, metric, and operator action.
