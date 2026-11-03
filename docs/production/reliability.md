# Reliability and incident response

Reliability means the service behaves acceptably for users over time. Define
“acceptable” with service-level indicators and targets before choosing
infrastructure.

## Failure budget

If the target is 99.9% availability, the remaining 0.1% is the error budget.
The budget makes trade-offs explicit: repeated budget exhaustion should slow
risky changes and prioritize reliability work.

## Recovery design

For each critical dependency, document:

- how failure is detected;
- whether the request retries, degrades, or fails;
- what data may be lost;
- how to restore service;
- how to verify recovery.

Backups are only useful when restoration is tested. Define recovery point
objective (how much data may be lost) and recovery time objective (how long
restoration may take), then exercise the procedure.

During an incident, stabilize first, communicate impact and next update time,
preserve evidence, and write a blameless postmortem afterward.
