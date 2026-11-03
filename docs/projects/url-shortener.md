# Project: URL shortener

Build a service that accepts a long URL and returns a short code that
redirects to it.

## First version

- `POST /links` creates a link;
- `GET /:code` redirects;
- a unique database constraint protects the code;
- malformed URLs return a validation error;
- redirects have a bounded lookup timeout.

## Production questions

- Should codes be random, sequential, or user-provided?
- Are deleted links immediately reusable?
- How are clicks counted without delaying redirects?
- What is cached, and how is a changed destination invalidated?
- How do you prevent code enumeration and abusive redirects?

Add request tests, a database constraint test, and an incident scenario for a
traffic spike.

## Suggested schema

```text
links
  id
  code              unique, indexed
  destination       validated URL
  owner_id          nullable foreign key
  disabled_at       nullable
  created_at

click_events
  id
  link_id           foreign key
  occurred_at       indexed
  request_id
```

Keep analytics asynchronous so the redirect path has one primary lookup and
does not depend on an analytics database. Decide how much IP or user-agent
data is retained and why.

## Expert extension

Add custom domains, abuse detection, expiration, preview metadata, and
multi-region reads. For each extension, document the new trust boundary,
consistency requirement, cache key, migration sequence, and rollback plan.
