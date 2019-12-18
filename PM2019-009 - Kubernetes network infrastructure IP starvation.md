# Kubernetes network infrastructure IP starvation

## Summary

TBD. Something about SRE team in different timezones.

## Timeline

All times in CET.

| Time  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Event |
| ----------------- | --- |
| 2019-11-29 12:00+ | Proactively scaling up Hellman Kubernetes cluster from 7 to 10 nodes, to ensure sufficient capacity for team migrations |
| 2019-11-30 08:24 | Automated warning from AWS, that the load balancer for Hellman Kubernetes ingress, were "unable to scale up because there are not enough free IP addresses in its subnets" |
| 2019-11-30 09:00 | Started working on a long-term solution |
| 2019-11-30 10:30 | Scaling down cluster by 2 nodes (to 8) to free up IP addresses |
| 2019-12-02 04:30 | Scaling down cluster by 1 node (to 7) to free up IP addresses |
| 2019-12-03 | Surge of new pods deployed (100+) rendering the issue exigent |
| 2019-12-04 03:00 | Solution completed, handed over to team in DK for testing and verification |
| 2019-12-04 09:30 | Asking teams to temporarily scale down deployments to free up IP addresses |
| 2019-12-05 03:30 | Started replacing Hellman Kubernetes network infrastructure and scaling up cluster to 12 nodes |
| 2019-12-05 05:30 | Forcefully killing nodes to free up IP addresses, as we were now running so low, we were not able to reconfigure load balancer |
| 2019-12-05 06:00 | Network infrastructure replacement completed |
| ... | Some Kubernetes daemon sets not scheduled, due to pod scheduling storm |
| 2019-12-05 07:00 | All systems verified operational |
| 2019-12-05 12:00 | Pull request to review changes unexpectedly triggers Hellman Kubernetes cluster pipeline, ungracefully reverts changes to infrastructure (see separate post-mortem) |

## Contributing Factors

- No monitoring of network subnet capacity
- Lack of revision of deployed infrastructure, due to heavy focus on developing new features
- Not possible to add availability zones to existing Kubernetes EKS clusters, complicating a long-term solution to IP starvation
- Not possible to update load balancer configuration (to fix the problem), due to near 100% IP address utilization in subnets
- One single Kubernetes node autoscaling group across AWS availability zones limiting flexibility
- We have announced sunset dates for other Kubernetes clusters
- Teams have recently been encouraged teams to migrate to Hellman

## Lessons Learned

TBD.

## Action Items

- For every resource deployed, consider the capacity, how to monitor it and how to scale
- Separate Kubernetes node autoscaling groups per AWS availability zone
- Implement priority-classes for critical workloads
- Consider separate prioritisation of SRE tasks