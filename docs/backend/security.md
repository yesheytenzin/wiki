# Backend security

Security is a property of boundaries and defaults:

- Validate and constrain input; never interpolate it into SQL or shell commands.
- Use parameterized queries and framework escaping.
- Hash passwords with a modern password-hashing algorithm.
- Apply authorization to objects, not just routes.
- Keep secrets out of the repository and logs.
- Set secure cookie flags and protect state-changing browser requests.
- Rate-limit abuse-prone endpoints and make limits observable.
- Patch dependencies and review their permissions.

Threat-model the valuable assets, trust boundaries, abuse cases, and recovery
actions before adding a feature. A secure design also explains how operators
detect and contain misuse.

## Request checklist

For each endpoint, ask:

1. Who can call it?
2. Which records can they read or change?
3. What is the maximum input size and rate?
4. Can a request be replayed?
5. What information appears in errors and logs?
6. How are suspicious calls detected and blocked?

Security failures are often authorization failures rather than injection
failures. Test a valid user accessing another user's resource, an expired
credential, a missing tenant scope, and an administrator-only action.
