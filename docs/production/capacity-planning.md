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
