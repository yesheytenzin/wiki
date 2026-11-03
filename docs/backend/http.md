# HTTP and web architecture

HTTP is a stateless request/response protocol. A backend should make method
semantics, status codes, caching headers, timeouts, and limits explicit.

## Request anatomy

A request has a method, target, headers, and optional body. A response has a
status code, headers, and optional body. Headers carry metadata such as
content type, authorization, caching policy, and correlation IDs.

The application should parse and validate the body before using it. Treat
everything crossing the network as untrusted and potentially incomplete.

| Concern | Example |
| --- | --- |
| Safe method | `GET` reads without changing server state |
| Idempotent mutation | `PUT` can be repeated to reach the same state |
| Client error | `422` means the request is understood but invalid |
| Server error | `503` means temporary unavailability is possible |
| Boundary protection | Timeouts, body-size limits, and rate limits |

A request may cross a browser, CDN, load balancer, application, database, and
third-party service. Assign a timeout at every network hop and propagate a
request ID so one user action can be traced across those hops.

## Idempotency and retries

`GET`, `HEAD`, and `OPTIONS` should not change server state. `PUT` and `DELETE`
are intended to be idempotent, but the application must still implement that
property. `POST` is not automatically safe to retry: use an idempotency key
when a client may repeat a payment, order, or webhook request.

## Practice

Use `curl` to inspect a real response:

```sh
curl -i -H 'Accept: application/json' https://example.com/api/orders
```

For a Rails endpoint, document the valid request, successful response,
validation error, authentication failure, and server failure separately.
