# Hellman Kubernetes IaC accidentally reverted

## Summary

TBD. Something about SRE team in different timezones.

## Timeline

**Date**: 2019-12-05  
**Timezone**: CET 

| Time | Event |
| :---: | --- |
| 04:00 | Team in US manually executed Terraform to update Kubernetes network infrastructure |
| 07:00 | Coordinate and hand-over code changes and clean-up procedure between team in US and DK |
| 10:00 | Team in DK reviews and revise handed-over code and procedure |
| 12:00 | Pull request to review changes unexpectedly triggers Hellman Kubernetes cluster pipeline, ungracefully reverts changes to infrastructure |
| 12:05 | Pull request updated with new infrastructure code |
| 12:10 | Corrective action delayed due to Terraform state lock |
| 12:15 | State lock forcefully cleared |
| 12:25 | Hellman Kubernetes cluster pipeline re-queued and finishes successfully |
| 12:30 | Some Kubernetes daemon sets not scheduled, due to pod scheduling storm |
| 12:35 | Pods manually deleted on fully subscribed nodes, enabling daemon sets to be scheduled |
| 12:40 | All systems verified operational |

## Contributing Factors

- Priority-classes not implemented
- Kubernetes pipeline unexpectedly triggered
- Terraform state locked, while no operation was taking place. Remediation steps unclear.

## Lessons Learned

TBD.

## Action Items

- Implement priority-classes for critical workloads
- Apply sane repo branch protection policies
- Apply sane pipeline triggers
- Run `terraform plan` in CI step to run on commits, to get information about consequence of applying
- Document procedure for breaking Terraform state locks
