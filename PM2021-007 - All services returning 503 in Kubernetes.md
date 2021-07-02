# All services returning 503 in Kubernetes

## Summary

We experienced another 503 incidents for all services on July 2. The symptoms were similar to other 503 incidents in the past few days,
and coincided with our daily scheduled execution of the EKS pipeline. However, it turned out to be another root cause than seen on June 30.

Investigations showed that the issue had started on June 15, and this coincided with an update to the Terraform AWS provider used. The provider behavior changed,
and this led to tearing down the association between the load balancer and the target groups. Later in the pipeline run, the association was re-established, but in the interim period status 503 messages was being thrown.

We have now changed lifecycle management for the AWS Autoscaling Groups, which preliminary testing indicates has fixed the problem. We will keep a close eye on the next few pipeline runs, and manually and repeatedly monitor service availability.

## Timeline

All times in UTC.

| Time             | Event                                                                                       |
| :--------------- | ------------------------------------------------------------------------------------------- |
| 2021-07-02 07:32 | Daily pipeline run starts modifying the AWS Autoscaling Groups. (problem started)           |
| 2021-07-02 07:37 | The first autoscaling groups' modifications finished.                                       |
| 2021-07-02 07:39 | A user reported seing 503 for all services.                                                 |
| 2021-07-02 07:40 | Automatic alarm received on Slack about that the load balancer targets not being available. |
| 2021-07-02 07:42 | Target group reattached to load balancer by the daily pipeline (resolved).                  |

## Contributing Factors

- On June 15 we upgraded the AWS Terraform provider to 3.45. This version contained some changes to the deployment of AWS Autoscaling Groups, where it became aware of changes to the load balancing target groups.
- Almost identical major incident happened earlier in the week, but for a different root cause. See previous postmortem.

## Action Items

- Enhance daily pipeline run with a synthetic monitoring during execution of the pipeline.
- If possible observe the next daily run of the pipeline if we have fixed a major issue.

## Lessons Learned

- Search for relevant provider upgrade documentation. This page mentions this issue: <https://registry.terraform.io/providers/hashicorp/aws/latest/docs/guides/version-3-upgrade#resource-aws_autoscaling_group>
- Several root causes can display similar sympthoms.

## Attendees

- RR, PW, AN

## Stats

- Category: Kubernetes
- Time to detection: 8 minutes
- Time to recovery:  10 minutes
