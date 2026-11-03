# Writing a postmortem

A postmortem is a learning document, not a blame document. Keep it factual,
blameless, and specific enough to produce engineering work.

## Template

```text
Incident:
Impact:
Detection:
Timeline:
Root cause:
Contributing factors:
Resolution:
What went well:
What went poorly:
Preventive actions:
Owners and due dates:
```

Separate the trigger from the conditions that made the incident possible.
Prefer actions that change a system, test, alert, or runbook over “be more
careful.” Close the loop by linking each action to an owner and verifying it.

## Investigation method

Build a timeline from deploys, alerts, logs, metrics, traces, and operator
actions. Record facts separately from hypotheses. Useful questions include:

- When did the first user-visible symptom begin?
- Which population was affected?
- What changed shortly before the incident?
- Why did detection work or fail?
- Which safeguard limited the blast radius?
- Why was recovery possible or difficult?

Avoid changing production repeatedly while evidence is still being gathered.
Capture a known-good comparison and preserve relevant log or metric windows.

## Action quality

Strong actions have an owner, due date, and verification:

| Weak action | Stronger action |
| --- | --- |
| Be more careful | Add a migration compatibility check to CI |
| Monitor it better | Alert on queue age above the user-impact threshold |
| Fix the bug | Add a regression test and a bounded repair job |
| Improve communication | Add an incident role and update cadence to the runbook |

Track follow-up work until it is deployed and exercised. A postmortem that
does not change detection, prevention, or recovery is only a narrative.

## Practice

Write a postmortem for a failed migration, a duplicate notification, and a
database outage. For each one, identify the trigger, latent conditions, blast
radius, mitigation, root cause, and three verifiable preventive actions.
