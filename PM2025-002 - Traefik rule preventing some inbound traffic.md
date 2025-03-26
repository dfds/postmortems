# Traefik Rule Preventing Some Inbound Traffic

## Summary

A change was pushed to fix the Traefik dashboard which inadvertently caused inbound traffic intended for the /api sub-path of any applications served to be directed to the dashboard instead of the intended application. This caused 404 errors for the affected applications using that sub-path. The change took effect later than expected due to an issue caused by the combination of a known issue and an unintended upgrade to a system component, which likely delayed detection. Once identified the issue was resolved by rolling back the Traefik change.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2025-03-17 16:06 | Kubernetes node entered NotReady state due to an ongoing issue |
| 2025-03-17 16:09 | Pipeline triggered to resolve issue with Traefik dashboard |
| 2025-03-17 16:43 | Pipeline failed due to an unintended update to the aws-ebs-csi-driver being included with the Traefik change which failed because of the NotReady node, meaning Traefik change did not take effect  |
| 2025-03-17 17:38 | Failed node terminated and evicted from cluster  |
| 2025-03-17 17:41 | Pipeline re-run  |
| 2025-03-17 17:53 | Pipeline run completed. Traefik change successfully applied  |
| 2025-03-17 23:42 | Cloud Engineering member informed of ingress problem for an individual workload |
| 2025-03-18 00:22 | Issue successfully identified. Began working on rollback of Traefik change |
| 2025-03-18 00:26 | Pipeline triggered for rollback |
| 2025-03-18 00:39 | Pipeline run completed |
| 2025-03-18 00:41 | Confirmed expected behaviour appeared resumed. Reached out to customer to confirm resolution |
| 2025-03-18 00:42 | Customer confirmed issue resolved |

## Contributing Factors

- A node entered a NotReady state due to an ongoing issue but this was not seen
- The Traefik change included a rule that would incorrectly direct traffic from other applications using a certain path (/api) to the Traefik dashboard and return 404 on those requests
- An unintended change to a DaemonSet was included with the intended Traefik change which caused the pipeline to fail
- The Traefik change was not applied at the indended time due to the failed pipeline
- The change took effect after typical working hours
- There is no test/alert to capture traffic to that particular path, even though we do test for successful sub-paths
- One of our components was affected but we did not have a test/check for it
- We don't have any applications that use the /api path in non-prod environments so we didn't catch the issue in testing
- An incorrect ingress rule in one Kubernetes namespace can potentially hijack traffic from another namespace and we have no method of preventing this in place
- We do not offer 24/7 support

## Action Items

- Continue to attempt a resolution for the NodeNotReady issues
- Investigate using a PolicyEngine to prevent conflicting routes in Traefik
- Investigate solutions that would show conflicting routes in Traefik via API or metrics
- Prevent EKS Add-ons from being included with other changes
- Add an additional Traefik test for /api path in our integration tests
- Align SLA's with business and support & operations

## Lessons Learned

- We need to improve our test coverage
- We should be more careful when making seemingly innocuous changes at the end of a working day
- We should be more aware of unintended changes
- There are unclear agreements in place for out-of-hours support with the business from Cloud Engineering

## Attendees

- RF SC AN MN EC SP SD AF

## Stats

- Category: Kubernetes
- Time to detection: 371 minutes
- Time to recovery: 389 minutes
