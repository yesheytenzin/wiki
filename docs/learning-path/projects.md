# Project sequence

Build these in order. Keep each project small enough to finish, test, deploy,
and explain.

| Project | Skills exercised |
| --- | --- |
| Ruby CLI journal | Objects, files, errors, unit tests |
| Rails notes API | Routing, CRUD, PostgreSQL, request specs |
| Authenticated API | Sessions or tokens, authorization, validation |
| URL shortener | Unique constraints, redirects, caching, analytics |
| Notification service | Background jobs, retries, idempotency |
| Rate-limited API | Redis or database counters, headers, failure behavior |
| Production Rails app | Docker, CI, metrics, logs, backups, runbooks |

For every project, write a short design note covering data ownership,
invariants, failure modes, and how you would roll back a bad deployment.

## Project completion standard

Do not consider a project complete when the happy path works. A complete
project has:

- a README explaining local setup and architecture;
- migrations and constraints for important invariants;
- unit, request, integration, and failure-path tests;
- authentication and authorization where data is private;
- bounded input, pagination, timeouts, retries, and idempotency;
- structured logs, metrics, and a health check;
- a deployment procedure, rollback plan, and backup/restore note;
- one load result and one postmortem for an injected failure.

For an expert-level project, publish an architecture decision record comparing
at least two approaches. Explain why the chosen design is sufficient now and
what signal would justify changing it.
