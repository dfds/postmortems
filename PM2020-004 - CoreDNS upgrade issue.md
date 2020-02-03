# CoreDNS upgrade issue

## Summary

On Thursday 5 December 2019,

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

- Action plan for checking release notes from both source and vendor in case of upgrading components / add-ons
