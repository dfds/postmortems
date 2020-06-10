# ALB instance attachments removed

## Summary

On Tuesday 9th of June 2020, a planned maintenance for the Hellman Kubernetes cluster related to seperate auto-scaling groups per availability zone caused the ALB's (AWS Application LoadBalancer), both the authenticated and not authenticated one, to remove all attached instance i.e. Kubernetes worker nodes. This caused all services running inside the cluster to become unavailable from outside the cluster.

When alerted, the SRE team quickly found the root cause and applied a quick fix, by manually attaching the instances to the ALB's. Afterwards a permanent fix was implemented within the hour.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2020-06-09 11:00 | Maintenance for changing Hellman auto-scaling groups started |
| 2020-06-09 12:20 | Terraform changes applied and the ALB instance attachments removed |
| 2020-06-09 12:25 | CloudWatch alert detects high count of 5XX for the ALB's |
| 2020-06-09 12:28 | Issue with no ingress traffic detected and verified |
| 2020-06-09 12:35 | Issue mitigated with manual attaching instances to ALB's |
| 2020-06-09 12:35 | Issue mitigated with manual attaching instances to ALB's |
| 2020-06-09 13:13 | Issue resolved and permanently fixed |

## Contributing Factors

- Test clusters does not validate ingress traffic via ALB's
- Playbooks for maintenance missing a step
- Maintenance window moved too close to lunch break
- Alerts explode when running "disruptive" maintenance
- General alert noise too high again
- Only alert by proxy for ALB attached instances

## Lessons Learned

- Genearl alert noise is still too high
- Missing alert on  health/unhealthy ALB targets
- Validate ingress via ALB for test clusters

## Action Items

- Implement alerts for ALB healthy/unhealthy targets. This would give a more precise and more instant notice
- Bring down alert noise even more
- Implement resolve host name for Goldpinger
