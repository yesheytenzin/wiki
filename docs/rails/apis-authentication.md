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

## Authentication choices

| Mechanism | Strength | Main design concern |
| --- | --- | --- |
| Server session | Easy revocation and small requests | Session storage and CSRF |
| Signed token | Useful across services | Expiry, revocation, and leakage |
| API key | Simple service identity | Rotation and narrow scope |
| OAuth/OIDC | Delegated identity | Redirect, state, nonce, and claims |

Do not treat authentication data as authorization. A valid token can still be
for the wrong tenant, resource, action, or role.

## Authorization model

Authorize the actor against the object and operation:

```ruby
def show
  order = current_user.orders.find(params[:id])
  render json: order
end
```

Scoping the lookup prevents a record from another user becoming reachable.
For more complex rules, make the policy explicit and test allowed, denied,
cross-tenant, and administrator cases.

## Error and replay behavior

Use a consistent error envelope:

```json
{
  "error": {
    "code": "invalid_input",
    "message": "email is invalid",
    "request_id": "req_123"
  }
}
```

Avoid revealing whether sensitive accounts exist. For mutating requests,
support idempotency keys when clients can retry after a timeout. Store the key
with the authenticated actor and operation result; do not let one actor replay
another actor's operation.

## Security review

Check secret storage, token transport, cookie flags, password hashing,
rotation, logout/revocation, brute-force limits, audit events, and logs.
Threat-model account recovery separately: it is often more privileged than
normal login.
