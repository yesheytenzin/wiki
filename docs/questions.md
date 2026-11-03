# Questions for you

Use these as review prompts. Explain the answer in your own words before
checking documentation.

- What is the difference between a 400, 401, 403, 404, and 422 response?
- Which database constraint protects this invariant if two requests race?
- What makes a background job safe to retry?
- Where should authorization happen, and how do you test it?
- What evidence would distinguish a slow query from a slow downstream service?
- What happens to a transaction when the process loses its database connection?
- Which cache entries can be stale, and what is the invalidation strategy?
- How would you deploy a schema change without breaking the previous release?
- What is the difference between a GraphQL query, mutation, and subscription?
- Where should GraphQL authorization run, and how do you prevent field leakage?
- How would you detect and fix a GraphQL N+1 query?
- Which consistency guarantee does this endpoint need, and why?
- What is the capacity limit of the database connection pool?
- What happens when a retry succeeds remotely but times out locally?
- Which metric would tell you that queue consumers are falling behind?
- What would you remove first from a system under overload?
- What is the single reason this class should change?
- Which variation deserves an extension point, and which should remain a
  simple conditional?
- Does this adapter honor the same inputs, outputs, and failures as the real
  implementation?
- Which client is forced to depend on methods it does not use?
- Which high-level policy currently constructs or knows about an infrastructure
  detail?
