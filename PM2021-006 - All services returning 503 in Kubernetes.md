# All services returning 503 in Kubernetes

## Summary

On Wednesday 30th June 2021 a scheduled  upgrade to Kubernetes from v1.19 to v1.20 was performed.  As part of this upgrade we also changed the mechanism used to deploy EKS Add-Ons from manual to managed.

A poor release strategy meant that a change which would have mitigated the issue seen was not put into production.  This resulted in a second occurence of all of the ALB targets being detatched, and in turn all Services within Kubernetes to return status 503 messages.

## Timeline

All times in UTC.

| Time             | Event                                                                                                    |
| :--------------- | -------------------------------------------------------------------------------------------------------- |
| 2021-06-30 08:32 | Kubernetes upgrade commenced.                                                                            |
| 2021-06-30 09:59 | Infrastructure Modules release 0.2.119.                                                                  |
| 2021-06-30 10:00 | Status 503 messages witnessed.                                                         |
| 2021-06-30 10:04 | Target group manually reattached to ALB's (resolved).                                                    |
| 2021-06-30 10:26 | Infrastructure Modules release 0.2.220 contains fix that sets a deregistration timeout on target groups. |

## Contributing Factors

- Release strategy and communication around releases that stay long in the pipeline has room for improvement.
- We had seen this issue before, and knew the fix for it, but the hotfix had not been applied to the cluster.

## Action Items

- Agree on a sustainable way to release both planned features and hotfixes from master.
- Clear communication if merges breaks the master branch ability to release hotfixes.
- Ensure that alerting in our sandbox clusters is configured to broadcast alerts to a test Slack channel so it aligns more with what we see in Production.

## Lessons Learned

- Quickly address the actions in postmortems (since the same issue was covered in the previous postmortem).

## Attendees

- RF, PW, AN, WC

## Stats

- Category: Kubernetes
- Time to detection: 1 minutes
- Time to recovery:  5 minutes
