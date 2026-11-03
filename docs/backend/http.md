# HTTP and web architecture

HTTP is a request/response protocol. A backend should make method semantics,
status codes, caching headers, timeouts, and limits explicit.

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
