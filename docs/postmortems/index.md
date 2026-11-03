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
