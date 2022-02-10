# Two Kuberneste nodes were down

## Summary

Two Kuberneste nodes were down. Several pods were stuck in Terminating state
on both nodes.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2022-02-08 12:05 | **Detection:** Prometheus fails to start after upgrade. |
| 2022-02-08 12:08 | Developer reports that Prometheus is down. |
| 2022-02-08 12:15 | We discover Prometheus' PVC is taken by other pod |
| 2022-02-08 12:15 | We discovered two nodes were in NotReady state |
| 2022-02-08 12:16 | Describing the nodes did not reveal why they were NotReady. CPU and Memory looked fine. |
| 2022-02-08 12:17 | We attempted to uncordon the nodes. |
| 2022-02-08 12:17 | One node didn't even try to uncordon. |
| 2022-02-08 12:18 | The other node said it was currently uncordoning, but nothing happened |
| 2022-02-08 12:22 | We used AWS CLI to mark the nodes as unhealthy. |
| 2022-02-08 12:25 | Two new EC2 instances were spawned, and the two unhealthy ones were terminated. |
| 2022-02-08 12:25 | Incident raised for the issue |
| 2022-02-08 12:48 | **Recovery:** We were able to start Prometheus because the the PVC was available again. |

## Contributing Factors

- Prometheus' PersistentVolumeClaim were already taken by other pod.

## Action Items

- Assess if the current EC2 instances are sized correctly to service the current workload.
- Assess if heavy resource consumers like Prometheus be given dedicated nodes?

## Lessons Learned

- Prometheus is likely to cause node instability in the future too unless we find a permanent solution to the resource issues.
- Raise an incident quicker, and not assume we could have started Prometheus faster.

## Attendees

- AN
- EC
- JS

## Stats

- Category: Kubernetes
- Time to detection: 0 minutes
- Time to recovery: 43 minutes
