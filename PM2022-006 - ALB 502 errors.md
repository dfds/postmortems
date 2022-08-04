# ALB 502 Errors

## Summary

A team reports small amount of randomly distributed HTTP 502 errors showing up in their logs. Further investigations showed that these errors were only present in the ALB access logs, and not in the Traefik access logs.

## Timeline

All times in UTC.

| Time                | Event                                                                                                      |
| :------------------ | ---------------------------------------------------------------------------------------------------------- |
| 2020-12-21 HH:mm    | Created bulk-apply-limitrange.sh to set limitranges on most namespaces. Afterwards run manually on demand. |
| 2021-09-10 HH:mm    | Traefik v2 went live in its own namespace                                                                  |
| 2021/22-??-?? HH:mm | bulk-apply-limitrange.sh was run manually without excluding the traefik namespace                          |
| 2022-05-?? HH:mm    | Detected on unknown date in May                                                                            |
| 2022-05-25 HH:mm    | CE started investigating it much deeper than previously                                                    |
| 2022-06-02 HH:mm    | CE discovered that the traefik pods were out of memory (OOM) killed                                        |
| 2022-06-02 HH:mm    | We found out that the traefik namespace had limitranges applied                                            |
| 2022-06-02 HH:mm    | The limitrange in the traefik namespace was deleted                                                        |
| 2022-06-07 08:30    | Exclude Traefik namespace from limitranges in bulk-apply-limitrange.sh script                              |
| 2022-06-10 HH:mm    | By looking at the logs, we confirmed that the problem was solved.                                          |

## Contributing Factors

- No lifecycle management of limitranges for new namespaces and any excluded namespace.
- Memory leak in Traefik
- Since the request never reached Traefik we were blind to the problem being in the Traefik pod.
- Weak signals. The were few 502 errors, and it happened very infrequently.

## Action Items

- Better monitoring of namespaces.
- Better monitoring of OOM in pods.
- Better governance of excluded namespaces.
- Automate the limitranges for namespaces.

## Lessons Learned

- Automate more. Avoid tactical handheld solutions.

## Attendees

- MH
- WC
- AN

## Stats

- Category: Kubernetes network
- Time to detection: N/A
- Time to recovery: N/A
