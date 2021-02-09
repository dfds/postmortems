# KIAM service disruption due to upgrade

## Summary

On 17. November, KIAM on Hellman was upgraded from 3.5 to 3.6, by applying a new version (`5.10.0`) of its _Helm chart_ - the manifest used to deploy the application and the Kubernetes resources it depends on. KIAM is the glue that enables Kubernetes workloads to authenticate transparently against AWS resources, such as connection database backends, S3 buckets etc.

The new Helm chart used [more inclusive naming][kiam-pr], changing one of the KIAM argument names from `WhitelistRouteRegexp` to `AllowRouteRegexp`. This means part of our configuration, necessary to support AWS PowerShell cmdlets, was now being ignored.

We updated the configuration to use the new argument name and verified in a sandbox environment that the argument was now interpreted and applied by the Helm chart again. Unfortunately we failed to verify that KIAM was actually running in the sandbox environment.

Shortly after the change was rolled out to production, we received alerts that KIAM was down. Upon closer inspection, the KIAM agents were crashing, because the new argument name passed by the updated Helm chart, was not actually known to any releases of KIAM at the time.

We rolled back to the previous version of the Helm chart (`5.9.0`), which still included KIAM 3.6 but not the updated arguments (that are incompatible with the current release of KIAM).

## Timeline

All times in CET.

| Time              | Event                                              |
| :---------------- | -------------------------------------------------- |
| 2020-11-17 10:45~ | KIAM upgraded, breaking support for AWS PowerShell |
| 2020-11-23 14:11  | "Fix" applied, KIAM service fully down             |
| 2020-11-23 14:41  | Helm chart rollback, KIAM service restored         |

## Contributing Factors

- AWS PowerShell with KIAM seems to be primarily used by Cloud Engineering, and only for a single component
  - Our alerting threshold for this service was set too high to pickup on this
  - Since no other teams seems to use it, the initial problem went unnoticed for too long

## Lessons Learned

- Be more careful to test the service end-to-end, when verifying fixes in sandbox or QA

## Action Items

- Setup dashboard and alerting for elevated error rates for KIAM
  - See https://github.com/uswitch/kiam/blob/master/docs/METRICS.md.

[kiam-pr]: https://github.com/uswitch/kiam/pull/427

# Stats

- Category: Kubernetes
- Time to detection: 0 minutes
- Time to recovery: 30 minutes

Notes: The stats above is for when we had a full KIAM outage.
