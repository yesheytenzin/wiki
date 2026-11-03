# Capacity planning

Capacity planning connects user demand to finite resources. Estimate before
you scale and verify with measurements.

## Workload model

Write down requests per second, payload size, read/write ratio, peak factor,
queue arrival rate, and acceptable latency. Separate average traffic from
burst traffic; averages hide the peaks that exhaust pools.

## Little's Law

For a stable system:

```text
concurrency = arrival rate × average time in system
```

If 100 requests per second each take 0.2 seconds, the system has about 20
in-flight requests on average. This helps estimate workers, connections, and
queue capacity, but it does not replace load testing.

## Bottleneck method

1. Identify the first saturated resource.
2. Measure utilization, queueing, and error behavior.
3. Reduce work, improve locality, or add capacity.
4. Re-measure because the bottleneck may move.

Database connections, downstream quotas, memory, and queue workers are common
limits. Add headroom and define overload behavior: reject, shed optional work,
degrade, or queue.

## Connection budgets

Every process, worker, admin task, and migration may consume a database
connection. Calculate the maximum across the deployment rather than sizing
one process in isolation. A pool larger than the database can serve increases
waiting and failure rather than throughput.

The same applies to HTTP clients, file descriptors, provider quotas, and Redis
connections. Make limits visible and fail with a bounded error.

## Scaling choices

- **Vertical:** more CPU or memory; simple but eventually bounded.
- **Horizontal:** more processes or hosts; requires shared durable state and
  correct coordination.
- **Caching:** reduces repeated work; adds staleness and invalidation.
- **Asynchronous work:** smooths bursts; adds delay and retry semantics.
- **Partitioning:** spreads data or work; adds routing and operational cost.

Choose the smallest change that addresses the measured bottleneck. Estimate
cost, failure blast radius, and rollback before introducing infrastructure.

## Practice

Model a service at 100, 1,000, and 10,000 requests per second. Identify the
first bottleneck, calculate connection and worker needs, define overload
responses, and list the measurements required to validate the model.
