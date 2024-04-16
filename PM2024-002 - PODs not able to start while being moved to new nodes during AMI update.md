# PODs not able to start while being moved to new nodes during AMI Update

## Summary
During the planned AMI update we've been notified by PAX team that some of their PODs aren't launching and this causes downtime. Jan Wiberg also mentions that he "can't access anything". It is worth mentioning that some Production PODs are still running on single replicas, which isn't ideal.

We're trying to identify the root cause and how this can be avoided on the next node rollover. One candidate can be the Kubernetes QPS (queries per second) limit, which was identified by Emil C.

## Timeline

All times in UTC.

| Time             | Event                                                                                                                        |
| :--------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| 2024-04-09 8:40 | Noticed that strangely all nodes entered the "Ready,SchedulingDisabled" state at once. Only 5 new nodes where brought up, which is normal.                                                         |
| 2024-04-09 8:51 | PAX people reaching out that they have issues with PODs not starting back up and being affected in PROD.                                                     
| 2024-04-09 8:59 | We've checked their specific PODs and all seemed fine, but it looked like it did take a bit longer than normal. 
| 2024-04-09 9:05 | It is confirmed by PAX team that everything is working as expected.
| 2024-04-09 9:10 | Looking into the issue it seems we'be been hit by some Rate limit by Amazon ECS/ECR, however, this shouldn't had happened as we're not near the 1000 pulls/second.
| 2024-04-09 9:15 | All nodes have been rolled over and all PODs are back to their normal working state.
| 2024-04-09 9:20 | We've concluded that everything is running as expected and only PAX might have been affected by it.


## Contributing Factors

- Prod deployments are configured with only 1 ReplicaSet, which is not ideal
- It appeared that Amazon ECR rate limit kicked in even if this shouldn't had happened, however we'll looking into it whether it's not a Kubernetes limit.

## Action Items
- We tweaked/adjusted the internal Registry pull limit/Quota (QPS) on Kubernetes level
- We've advised the Teams to adjust their deployments not to use Single Replicas

## Lessons Learned
- We have the Kubernetes registry pull limit (QPS) that could create issues durring node rollovers
- Teams still haven't adopted our recommendations regarding Deployments
- Diversify our channels of communication regarding recommandation to other Teams in DFDS (Maybe a monthly Open demo/presentation inviting developer teams)
- An idea would be to separate staging from production that teams use so we can simulate how our changes affects workloads (multi-cluster)

## Attendees
AF, EC, SD, SC
## Stats
- Category: Kubernetes
- Time to detection: 10 minutes
- Time to recovery: 10 minutes
