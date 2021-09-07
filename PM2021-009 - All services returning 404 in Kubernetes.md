# All services returning 404 in Kubernetes

## Summary
Malformed IngressRoute manifest applied to Kubernetes triggered a bug in Traefik v2 causing no ingress traffic to be received nor routed. Due to the nature of the bug it first triggered after all the Traefik v2 pods got restarted.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2021-09-01 11:34 | Malformed IngressRoute applied to Kubernetes |
| 2021-09-06 07:30 | Automated Kuberentes node rollover started |
| 2021-09-06 08:00 | All Kubernetes services returning 404 |
| 2021-09-06 08:05 | Root cause investigation started |
| 2021-09-06 08:10 | ALB targets unhealthy |
| 2021-09-06 08:15 | ALB targets healthy after altering the healthcheck path |
| 2021-09-06 08:20 | All Kubernetes services still returning 404, but now it is Traefik v2 and not the ALB's |
| 2021-09-06 08:30 | Misc. investigation non conclusive |
| 2021-09-06 08:50 | Roll back to Traefik v1 as main ingress controller initiated |
| 2021-09-06 09:00 | Roll back performed by hand |
| 2021-09-06 09:08 | All ingress traffic working as intended |
| 2021-09-06 09:10 | Root cause discovered by manual deleting malformed IngressRoute and Traefik v2 bug verified |
| 2021-09-06 09:20 | Changes applied upstream |

## Contributing Factors

- [Traefik v2 bug](https://github.com/traefik/traefik/issues/8098)
- Traefik v2 failing gracefully causing misleading ALB issue investigation
- Traefik v2 official API schema not preventing application of malformed manifests

## Action Items

- Upgrade Traefik to v2.5 where bug is fixed and verify that it removes the bug
- Change Traefik v2 healthcheck path to something more robust
- Setup external liveness probe on ingress controller
- Workload segmentation might be an option to limit blast zones
- Look into monitoring the Traefik v2 API for rapid changes
- Look into smoketesting, during and after node rollover

## Lessons Learned

- Appointing an incident manager could have helped
- Record war room meeting
- Only written suggestions allowed from people not involved
- Keep log book
- Have a short initial round sharing thought, gut feelings etc.

## Attendees

- RR, EC, PW, WC

## Stats

- Category: Kubernetes
- Time to detection: 5 minutes
- Time to recovery: 68 minutes
