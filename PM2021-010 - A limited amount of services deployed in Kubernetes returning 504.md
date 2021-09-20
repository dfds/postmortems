# A limited amount of services deployed in Kubernetes returning 504

## Summary

**Traefik versions confirmed affected**: 1.7

Ingress object pointing towards a Secret with a data format not expected by Traefik resulted in Traefik being unable to forward requests for Ingress/Service' created after the introduction of the aforementioned change.

## Timeline

All times in UTC.

| Time             | Event                                                                                                                                               |
| :--------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2021-09-17 08:00 | Culprit Ingress object applied to cluster                                                                                                           |
| 2021-09-17 13:35 | First report of issue by user                                                                                                                       |
| 2021-09-17 13:43 | Started investigation. Asked user for additional information.                                                                                       |
| 2021-09-17 13:43 | From the investigation nothing out of the ordinary was discovered. Assumed user error at the time.                                                  |
| 2021-09-17 14:06 | Second report of the same issue from user in a different team                                                                                       |
| 2021-09-17 14:30 | Additional report by user from a third team                                                                                                         |
| 2021-09-17 14:48 | Potential culprit identified. Removed Ingress object, verified that Traefik forwarded to services that it didn't previously. Awaited user response. |
| 2021-09-17 14:49 | First user reports back that it works                                                                                                               |
| 2021-09-17 15:05 | Second user reports back that it works                                                                                                              |
| 2021-09-17 15:19 | Owner of the Ingress causing the issue contacted                                                                                                    |

## Contributing Factors

- There are GitHub issues with similarly reported errors that have been open for a while in the official Traefik GitHub repository. Fixes have been suggested, but thus far haven't been implemented

## Action Items

- Look into if this is also an issue in Traefik 2.X
- Look into if there is a way of monitoring irregular 504s with Prometheus/Grafana
- Get in touch with users of Hellman and explain the issue as well as how to avoid triggering it while it isn't fixed

## Lessons Learned

- Asked for more precise information from users could've lead to a quicker recovery time

## Attendees

- AN, EC, RF, PW

## Stats

- Category: Kubernetes
- Time to detection: 335 minutes
- Time to recovery: 408 minutes
