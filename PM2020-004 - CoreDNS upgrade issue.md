# CoreDNS upgrade issue

## Summary

On Monday 3 of February 2020, a planned upgrade of the Hellman Kubernetes cluster from version 1.13 to 1.14 caused a minor DNS related issue.
When upgrading the Kubernetes version, a series of components used by Kubernetes also needs to be upgraded along side of the Kubernetes cluster version.

One of these components, CoreDNS which handles internal cluster DNS services, had additional vendor specific changes which differed from the standard open source notes of the upgrade.
These vendor specific changes, when not implemented at the time of upgrade, caused the CoreDNS service to fail and crashloop.

When alerted, the SRE team investigated the crashing CoreDNS component, detected the difference between open source and vendor documentation and implemented the vendor specific fix to solve the issue.

## Timeline

All times in CET.

| Time             | Event                                                          |
| :--------------- | -------------------------------------------------------------- |
| 2020-02-03 11:21 | EKS 1.14 upgrade cause CoreDNS pods to crash loop              |
| 2020-02-03 11:52 | CoreDNS deployment manually downgraded, which solved the issue |
| 2020-02-03 13:30 | CoreDNS upgraded and issue permanently fixed                   |

## Contributing Factors

- Amazon specific release notes differs from official CoreDNS release notes.
- Node rollover requires hands-on, which removes focus from observing cluster state.
- Missing physical war room.

## Lessons Learned

- Components/Addons release notes needs more carefully studying both in source release notes and vendor specific implementation.
- Missing specific monitoring of core components like kube-proxy, CoreDNS and VPC CNI instead of monitoring by proxy.

## Action Items

- Action plan for checking release notes from both source and vendor in case of upgrading components / add-ons.
- We need monitoring of core services with direct health checks / metrics on these services instead of monitoring them by proxy checks / metrics.
