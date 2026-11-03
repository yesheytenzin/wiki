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
