# Deep study method

This wiki is meant to build understanding, not recognition. Reading a
definition is only the first pass. For every concept, move through the same
sequence:

1. **Name the problem.** What fails without this concept?
2. **Build the smallest model.** Draw the data, control flow, and boundary.
3. **Implement the simple version.** Prefer plain Ruby, SQL, or HTTP first.
4. **Break it deliberately.** Use invalid input, concurrency, timeout,
   duplication, and partial failure.
5. **Measure it.** Record latency, memory, query count, queue age, or error
   rate as appropriate.
6. **Compare alternatives.** Explain why the chosen design is sufficient.
7. **Operate it.** Add logs, metrics, recovery steps, and a rollback plan.
8. **Teach it.** Explain the invariant and trade-off without reading notes.

## The four questions every backend concept must answer

### What is the invariant?

An invariant is the condition that must remain true. Examples:

- an email identity is unique;
- a paid order cannot return to pending;
- a cursor returns a stable, non-duplicated sequence;
- a retried job does not charge twice;
- an unauthorized actor cannot read a private record.

If you cannot state the invariant, you cannot tell whether an implementation is
correct.

### Who owns the invariant?

Put each rule at the strongest boundary that can enforce it:

| Rule | Typical owner |
| --- | --- |
| Request has valid JSON shape | Transport/application boundary |
| Actor may perform an operation | Authorization policy |
| Order transition is valid | Domain operation |
| Email is unique under a race | Database constraint |
| Event is retried safely | Consumer and durable operation record |
| Service meets latency target | System design and operations |

Validation improves feedback; it does not replace a database constraint.

### What happens concurrently?

Ask what two requests, workers, deploys, or regions can do at the same time.
Model the interleaving that breaks the invariant. Then choose a unique
constraint, lock, optimistic version, idempotency key, isolation level, or
reconciliation process.

### What happens when it fails?

Classify failure as invalid input, authorization failure, transient dependency
failure, permanent failure, timeout with unknown outcome, or operator error.
Each category needs different retry, response, alert, and recovery behavior.

## Notes that demonstrate mastery

For each project or chapter, keep four short documents:

- **Architecture decision record:** alternatives, decision, consequences.
- **Threat model:** assets, actors, trust boundaries, abuse cases.
- **Load note:** workload, bottleneck, measurements, capacity limit.
- **Failure note:** injected failure, symptoms, mitigation, recovery, follow-up.

Do not memorize a pattern without recording when it should not be used. An
expert can explain the cost of an abstraction and the signal that would justify
replacing it.

## Mastery rubric

| Level | Evidence |
| --- | --- |
| Familiar | Can define the term and reproduce a basic example |
| Working | Can implement it, test failure cases, and debug it |
| Production | Can measure it, secure it, deploy it, and recover it |
| Expert | Can compare alternatives, predict failure, and teach the invariant |

The target of this wiki is the last two levels. Move on only after you can
explain a concept, implement a small version, and diagnose a broken version.
