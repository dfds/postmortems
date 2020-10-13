# Unhealthy node

## Summary

We saw issues with pods crashlooping and pending on one particular node. KIAM was one of the critical pods to go down. Potentially causing all of the pods running on that node to not be authenticated towards AWS.

We saw high memory usage on the node, in particular high usage from the Prometheus service (12 GB). The nodes have 16 GB currently.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2020-10-06 07:00 | Regular maintenance Kubernetes node roll over |
| 2020-10-06 08:00 | Pods, including KIAM, started crashlooping and become pending |
| 2020-10-06 09:00 - 11:00 | Ongoing investigation and troubleshooting to find root cause |
| 2020-10-06 11:30 | Rolled over sick node |
| 2020-10-06 12:00 | All healthy and issue resolved |

## Contributing Factors (findings)

- Prometheus high memory usage
    - ~12 GB of memory usage
    - ~100 GB of virtual memory usage
    - ~2.5 GB of free memory on the node
    - ~14.5 GB allocatable
- Container runtime crashed on node
    - "container runtime is down"
    - "skipping pod synchronization"
    - "PLEG is not healthy"
- Liveness/readiness probes failed
    - context deadline exceeded
    - connection refused

## Lessons Learned

We recorded the facts so we can revisit the issue, because we didn't learn that much to be honest.

## Action Items

- Create a query/dashboard/alarm detecting pod restarts grouped by node.
- Prioritize upgrading our kubernetes prometheus stack.