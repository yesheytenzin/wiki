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
