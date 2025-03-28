# Node system resource starvation and scheduling capacity

## Summary

A few Kubernetes nodes were entering NotReady state and evicting workloads. The fix used additional capacity on each node to prevent the issue from reoccurring but as a consequence the cluster became overprovisioned causing a small number of customer workloads to be stuck in Pending state.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2025-02-27 19:32 | First occurence of a node in NodeNotReady state |
| 2025-03-03 09:52 | Second occurence of a node in NodeNotReady state |
| 2025-03-03 11:04 | Third occurence of a node in NodeNotReady state |
| 2025-03-03 12:05 | Fourth occurence of a node in NodeNotReady state |
| 2025-03-03 13:05 | Fifth occurence of a node in NodeNotReady state |
| 2025-03-03 14:05 | Sixth occurence of a node in NodeNotReady state |
| 2025-03-03 15:33 | Seventh occurence of a node in NodeNotReady state |
| 2025-03-03 16:09 | Pushed potential fix (AMI update, systemReserved capacity increase) to master branch |
| 2025-03-03 16:19 | Potential fix started to apply to cluster via pipeline |
| 2025-03-03 16:50 | Pipeline finished - Potential fix successfully applied |
| 2025-03-03 16:55 | Checked everything seemed ok |
| 2025-03-03 18:53 | Post on Slack from a customer reporting their workload had been stuck "pending" for at least 30 mins |
| 2025-03-03 19:13 | Responded asking for some information regarding the workload |
| 2025-03-03 19:22 | Received the requested information |
| ~2025-03-03 19:30 | Investigated and found a wider capacity issue with the cluster |
| 2025-03-03 19:40 | Reached out to colleague for second opinion and received agreement/confirmation |
| 2025-03-03 20:01 | Triggered the pipeline for applying the capacity fix (additional nodes) |
| 2025-03-03 20:14 | Additional nodes become available |
| 2025-03-03 20:24 | Responded to customer saying the wider issue was fixed |


## Contributing Factors

- Availability of Ops team members
- We seem to lose some observability as the issue happens and logs/metrics do not get recorded
- We are missing alerting/baseline (elastic threshold) on pending/errored customer workloads
- Too much trust in our metrics/alerts covering everything, which they do not
- A previous memory utilisation alert threshold was raised from 85% to 90% which could have been a sign we missed
- The fix for the initial issue caused a new issue
- We are missing alerting for cluster capacity regarding requested CPU and memory

## Action Items

- Look into Grafana Cloud's Kubernetes integration alerts 
- Add alerting for cluster capacity with requested CPU and memory to catch future instances of this issue
- Look into cluster cronjobs as a potential cause of issue due to apparent almost hourly NodeNotReady state
- Add some monitoring to CronJob resource usage
- Investigate missing node metrics/logs and see if we can increase the frequency they are sent
- Write a wiki article on cluster capacity and pending workloads

## Lessons Learned

- We do not have sufficient monitoring/alerting on cluster capacity
- We have a need for horizontal scaling of nodes to prevent capacity issues


## Attendees

- EC RF SD MN SC AN AF SP

## Stats

- Category: Kubernetes
- Time to detection: 5280 minutes
- Time to recovery: 5840 minutes
