# Backend Wiki

A structured path from Ruby fundamentals to reliable Rails systems in
production.

This is a **learning reference**, not a framework glossary. Each page explains
the concept, shows the boundary where it matters, and ends with practical
questions or exercises. The organizing principle is the lifecycle of backend
work:

```text
input → application code → database/queue → response → operation
```

## Start here

- [Follow the learning path](learning-path/index.md)
- [Choose a project](learning-path/projects.md)
- [Review self-check questions](questions.md)
- [Look up a term in the glossary](glossary.md)

## Curriculum map

| Area | Outcome |
| --- | --- |
| 1. Ruby | Write clear, testable programs and understand object behavior |
| 2. Web and data | Understand requests, networks, storage, and security |
| 3. Rails | Build APIs with explicit application boundaries |
| 4. Architecture | Connect services safely through queues and contracts |
| 5. Production | Deploy, observe, debug, and recover backend systems |
| 6. Algorithms | Choose practical algorithms for latency and scale |

## How to read a page

Each detailed topic follows this pattern:

1. **Mental model** — what the concept does and what it does not do.
2. **Implementation** — Ruby, Rails, SQL, or HTTP examples.
3. **Trade-offs** — when the approach fails or becomes expensive.
4. **Operations** — what to measure, alert on, and recover.
5. **Practice** — an exercise or project connection.

Read the path in order for a curriculum, or use search when solving a specific
problem. The pages assume basic programming knowledge and progressively
introduce production constraints.
