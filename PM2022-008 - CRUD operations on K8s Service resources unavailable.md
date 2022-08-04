# CRUD operations on K8s Service resources unavailable

## Summary

CRUD operations on any K8s Service resource excluding exempted namespaces were unavailable due to an unresponsive webhook. More specifically, Kyverno was stuck in crash loop. This was resolved by updating to a newer Helm Chart version of Kyverno.

## Timeline

All times in UTC.

| Time                   | Event                                                                                                         |
| :--------------------- | ------------------------------------------------------------------------------------------------------------- |
| 2022-08-04 11:38       | Alerted by end-user team that they were having issues applying a Service resource to the affected K8s cluster |
| 2022-08-04 11:50       | Started looking into the alerted issue                                                                        |
| 2022-08-04 12:08       | Issue identified, started work on fix                                                                         |
| 2022-08-04 12:18       | Issue recreated in K8s sandbox                                                                                |
| 2022-08-04 12:26-12:?? | Fix deployed in K8s sandbox                                                                                   |
| 2022-08-04 13:06       | Fix deployed in affected production cluster, awaiting user feedback                                           |
| 2022-08-04 13:25       | User reporting back that the fix works                                                                        |

## Contributing Factors

- Kyverno not being kept up-to-date due to code freeze
- If the Kyverno ClusterPolicy's 'failurePolicy' was set to 'ignore' instead of the default 'fail', the issue wouldn't have occurred

## Action Items

- Monitoring on Kyverno
- Internal documentation on our Kyverno setup

## Lessons Learned

- Consider if we should change the failurePolicy on ClusterPolicy resources going forward

## Attendees

- RF
- EC
- 

## Stats

- Category: Kubernetes
- Time to detection: 12 minutes
- Time to recovery: 88 minutes
