# Two Kubernetes nodes went into NotReady state

## Summary

In the morning one Kubernetes node entered NotReady state, and was replaced by a new one.
In the afternoon the same thing happened to the new Kubernetes node.

The root cause was a Grafana Agent pod using a high amount of memory on a node. Hangfire pods were allocated to the same node causing a spiral of CrashLoopBackOff due to System OOM eventually making the whole node unresponsive.

## Timeline

All times in UTC.

| Time             | Event                                                                                                                        |
| :--------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| 2024-03-13 HH:mm | Short event description.                                                                                                     |
| 2024-03-13 10:01 | Node entered NotReady state. Requests: CPU at 78% and memory at 82%.                                                         |
| 2024-03-13 10:03 | Alert triggered on Slack.                                                                                                    |
| 2024-03-13 10:05 | Alert discovered, and investigations started.                                                                                |
| 2024-03-13 10:12 | Incident declared through statuspage.io.                                                                                     |
| 2024-03-13 10:13 | Attempted to SSH into the node, but it was unresponsive.                                                                     |
| 2024-03-13 10:19 | Node started responding again.                                                                                               |
| 2024-03-13 10:22 | Marked the EC2 instance as unhealthy and new node started.                                                                   |
| 2024-03-13 10:25 | CloudWatch showed a tenfold increase in EC2 network traffic leading up to the incident.                                      |
| 2024-03-13 10:30 | Cordoned and force drained the NotReady node.                                                                                |
| 2024-03-13 10:30 | Grafana Agent ran on the unhealthy node, and it had high memory usage.                                                       |
| 2024-03-13 10:36 | Terminated the unhealthy EC2 instance.                                                                                       |
| 2024-03-13 12:10 | Incident declared resolved through statuspage.io, but investigations continued.                                              |
| 2024-03-13 13:00 | Discovered that Grafana Agent was still down due to a newly applied LimitRange.                                              |
| 2024-03-13 13:02 | Removed LimitRange from the namespace and restarted the Grafana Agent pod.                                                   |
| 2024-03-13 13:49 | Concluded that a dev container using hangfire to trigger jobs, but fail with OOM. <br>Caused CrashLoopBackOff with new OOMs. |
| 2024-03-13 13:55 | Discovered RDS issues for the instance used by the rogue dev container.                                                      |
| 2024-03-13 13:55 | Found correlation between increased EC2 network traffic, RDS and the rogue dev container.                                    |
| 2024-03-13 14:?? | Scaled the rough dev container down to 0 replicas. EC2 network traffic went down to normal levels after a few minutes.       |
| 2024-03-13 14:?? | Scaled the rough dev container up to its original 3 replicas.                                                                |
| 2024-03-13 15:03 | Alert triggered on Slack. The new node entered NotReady state.                                                               |
| 2024-03-13 15:09 | Alert discovered.                                                                                                            |
| 2024-03-13 15:15 | Scaled the rogue dev container to 0 replicas again.                                                                          |
| 2024-03-13 15:18 | New node is added                                                                                                            |
| 2024-03-13 15:19 | Email sent to the team owning the rogue dev container.                                                                       |
| 2024-03-13 15:26 | Incident declared through statuspage.io.                                                                                     |
| 2024-03-13 15:52 | Development team acknowledged the problem, and confirmed they were working on a fix.                                         |
| 2024-03-14 09:20 | Incident closed.                                                                                                             |

## Contributing Factors

- Dev workloads mixed with prod workloads
- 'grafana-agent' Not running on a dedicated node group unlike the majority of our monitoring stack

## Action Items

- Move 'grafana-agent' to monitoring node group
- Add alerts for when bandwidth across the cluster reaches a certain threshold
- Investigate if we can set memory limits on grafana-agent Helm chart
- Investigate grafana-agent memory usage
- Ask Grafana support if we should use PVC for grafana-agent
- Consider pushing node logs to Loki with limited retention
- Create wiki page with common metrics/log queries to check when investigating issues with Kubernetes nodes

## Lessons Learned

- Follow up on action items from post-mortems
- Knowledge of useful metrics in CloudWatch haven't been properly shared with the team, making it harder to diagnose/investigate

## Attendees

- AF, AN, EC, RF, SC

## Stats

- Category: Kubernetes
- Time to detection: 4 minutes
- Time to recovery: 399 minutes
