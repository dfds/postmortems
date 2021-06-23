# All services returning 503 in Kubernetes

## Summary

On Tuesday 22nd June 2021, changes to the VPC CNI caused target groups to become detached from the ALB. This, in turn, meant that all inbound traffic failed to reach the cluster.

The changes to the VPC CNI were caused by refactoring and cleanup intended to allow EKS add-ons to be managed by native Terraform instead of kubectl.

## Timeline

All times in UTC.

| Time             | Event                                                          |
| :--------------- | -------------------------------------------------------------- |
| 2021-06-22 08:21 | VPC CNI replaced by the pipeline                               |
| 2021-06-22 08:29 | Issue detected as a result of alerts                           |
| 2021-06-22 08:30 | Investigation into issue commenced                             |
| 2021-06-22 08:32 | Issue reported by customer                                     |
| 2021-06-22 08:33 | Fix committed to revert infrastructure-modules reference       |
| 2021-06-22 08:38 | Pipeline re-triggered due to AWS API timeout                   |
| 2021-06-22 08:46 | Target groups reattached to the ALB by the pipeline (resolved) |

## Contributing Factors

- ALB Target Group de-registration delay was permanently lowered to improve node roll-over performance.
- Replacement of essential network component.

## Action Items

- Revert to default of 300 seconds for de-registration delay on the ALBs.
- Update roll-over script to temporarily set de-registration delay to zero on ALBs prior to roll-over.
- Create ingress test suite.

## Lessons Learned

- De-registration delay change mainly considered from performance perspective.
- Actively monitor critical pipeline executions.
- Ensure that everyone gets notification on alerts and reacts to them.
- Rephrase alert messages that are unclear.

## Attendees

- RF, PW, RR, AN, EC, SD, WC

## Stats

- Category: Kubernetes
- Time to detection: 8 minutes
- Time to recovery: 25 minutes
