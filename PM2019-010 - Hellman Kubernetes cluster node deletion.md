# Hellman Kubernetes cluster node deletion

## Summary

TBD.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2019-12-04 00:30 | Accidental execution of command that deleted all nodes in Hellman Kubernetes cluster |
| 2019-12-04 00:35 | Manually marked VMs as unhealthy, triggering automatic reprovisioning of instances |
| 2019-12-04 01:00 | Verified that new nodes joined cluster and most pods up and running again |
| ... | Some critical daemon sets (i.e. KIAM) not running, due to IP address starvation (see separate post-mortem) |
| ... | Applying built-in priority-classes to shedule critical components before other workload |
| 2019-12-04 01:30 | All systems operational |

## Contributing Factors

- Least-privilege in Hellman Kubernetes cluster not implemented
- IP address starvation in Hellman network infrastructure (see separate post-mortem)
- Priority-classes not implemented

## Lessons Learned

TBD.

## Action Items

- Implement least-privilege in Hellman Kubernetes cluster
- Implement priority-classes for critical workloads
