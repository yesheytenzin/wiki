# APIs and authentication

An API contract includes methods, paths, status codes, response shape,
authentication rules, and error semantics. Document it before adding clients.

Authentication answers **who** the caller is. Authorization answers **what**
that caller may do. Keep them separate and test both allowed and denied cases.

## API checklist

- Validate input at the boundary.
- Use server-side authorization for every protected resource.
- Avoid leaking whether sensitive records exist.
- Return stable error codes for clients and useful request IDs for operators.
- Make mutating operations idempotent when clients may retry them.
- Version breaking changes; do not silently change a field's meaning.

For browser sessions, protect state-changing requests against CSRF. For
tokens, define expiry, rotation, revocation, storage, and scope before coding.
