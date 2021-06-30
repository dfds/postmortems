# All services returning 503 in Kubernetes

## Summary

On Wednesday 30th June 2021 a scheduled  upgrade to Kubernetes from v1.19 to v1.20 was performed.  As part of this upgrade we also changed the mechanism used to deploy EKS Add-Ons from manual to managed.

A poor release strategy meant that a change which would have mitigated the issue seen was not put into production.  This resulted in a second occurence of all of the ALB targets being detatched, and in turn all Services within Kubernetes to return status 503 messages.

## Timeline

All times in UTC.

| Time             | Event                                           |
| :--------------- | ----------------------------------------------- |
| 2021-06-30 08:32 | Kubernetes upgrade commenced                    |
| 2021-06-30 09:59 | Infrastructure Modules release 0.2.119          |
| 2021-06-30 10:00 | Status 503 messages witnessed for a second time |
| 2021-06-30 10:04 | Target group manually reattached to ALB's       |

## Contributing Factors

- Poor release strategy/lack of coordination

## Action Items

- Arrange meeting to discuss release strategy an possible areas of improvement.
- Ensure that alerting in our sandbox clusters is configured to broadcast alerts to a test Slack channel so it aligns more with what we see in Production.

## Lessons Learned

-

## Attendees

- RF, PW, AN, WC

## Stats

- Category: Kubernetes
- Time to detection: 2 minutes
- Time to recovery:  3 minutes
