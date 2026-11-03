# API design

An API is a contract between independently changing clients and servers. Start
with a use case and document the contract before implementation.

## Contract checklist

- resource names and nesting;
- authentication and authorization;
- request fields, types, defaults, and limits;
- success and error status codes;
- pagination and ordering;
- idempotency behavior;
- rate limits and retry guidance;
- compatibility and deprecation policy.

Prefer additive evolution. If a change cannot be understood by an older
client, version or introduce a new endpoint rather than silently changing the
old contract.

## Pagination

Offset pagination is easy to understand but becomes expensive and unstable
when rows are inserted. Cursor or keyset pagination uses a stable ordered
column and is generally better for large, changing collections.

Always return enough metadata for a client to request the next page, and set a
maximum page size on the server.
