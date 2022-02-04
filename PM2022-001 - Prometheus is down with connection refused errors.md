# Prometheus is down with connection refused errors

## Summary

There were problems scheduling Prometheus due a k8s node being in NotReady state.
Other pods running on the same node was naturally also affected.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2022-01-25 10:21 | **Detection:** We received a lot of alerts about Prometheus i/o timeouts |
| 2022-01-25 10:23 | Discovered that one node was in NotReady state. Prometheus was running on this node. |
| 2022-01-25 10:25 | Security group was updated to allow SSH connections from Europarc to the nodes. |
| 2022-01-25 10:28 | Same issue reported by one developer. |
| 2022-01-25 10:30 | We were unable to SSH into the node. |
| 2022-01-25 10:49 | We started a full node rollover on the cluster. |
| 2022-01-25 11:25 | Node rollover completed. |
| 2022-01-25 12:51 | Another node entered NotReady state. Prometheus was running on this node. |
| 2022-01-25 13:20 | We set the EC2 instance to unhealthy, and that triggered a new node being started up, and workloads migrated. |
| 2022-01-25 13:30 | We opened up SSH connections to the node that now contained Prometheus in case we got a third incident. |
| 2022-01-25 14:33 | Third node entered NotReady state, and it was the node where Prometheus was running. |
| 2022-01-25 14:33 | We started doing diagnosis on the node (USE method)[^1] |
| 2022-01-25 14:50 | We migrated the Prometheus pod to another node. |
| 2022-01-25 15:00 | **Recovery:** Uncordoned the node that was in NotReady state. |
| 2022-01-25 15:14 | Found spikes in CPU by looking at Grafana dashboard. |
| 2022-01-25 15:44 | Found a kernel message: event_loop invoked oom-killer. |
| 2022-01-25 16:35 | Manually setting CPU and memory resource requests and limits on the Prometheus pod. |
| 2022-01-26 09:36 | CPU and memory resource requests and limits on the Prometheus pod implemented through IaC [^2] |

## Contributing Factors

- No resource requests or limits were set on the Helm chart that deploys the Prometheus pod.
- We wrongly assumed the Helm chart were setting resource requests and limits by default.

## Action Items

- Review if our pods have appropriate resource requests and limits set.

## Lessons Learned

- We need be better at following our incident handing process to improve efficiency and post-incident documentation.

## Attendees

- RR
- EC
- PW
- AN

## Stats

- Category: Kubernetes
- Time to detection: 0 minutes
- Time to recovery: 279 minutes [^3]

[^1]: USE method: Utilization, Saturation, Errors.
[^2]: IaC: Infrastructure as Code
[^3]: We had 3 incidents in one day, so the time to recovery was from the start of the first incident to the fix of the third incident.
