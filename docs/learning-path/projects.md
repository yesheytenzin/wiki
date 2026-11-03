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
