# TBD: Log4Shell

## Summary

TBD

## Timeline

All times in UTC.

| Time               | Event                                                                              |
| :----------------- | ---------------------------------------------------------------------------------- |
| 2021-12-10   00:00 | The Log4Shell vulnerability was published                                          |
| 2021-12-10   15:40 | We became aware of it during a call with Peter Taylor                              |
| 2021-12-10   16:10 | IT-Security published messaged on MS Teams and Slack about it                      |
| 2021-12-10   16:10 | Azure DevOps git repositories were searched for usage of Log4J                     |
| 2021-12-10   19:00 | People in CE was discussing internally about Log4J, and decided to advice S&O      |
| 2021-12-13   09:15 | Started investigating DFDS' usage of ElasticSearch/OpenSearch on AWS               |
| 2021-12-13   11:45 | Jan and John discussing a collaboration with S&O                                   |
| 2021-12-13   15:50 | Started scanning Docker images used on Hellman                                     |
| 2021-12-14   ??:00 |                                                                                    |
| 2021-12-14   ??:00 |                                                                                    |
| 2021-12-??   ??:00 | Scanning all images in ECRs not covered by the first Docker layer scan             |
| 2021-12-??   ??:00 | Communication with the architects started                                          |
| 2021-12-16   14:00 | Scanning EC2 instances started                                                     |
| 2021-12-17   13:00 | Four EC2 servers found with critical vulnerabilities, but not related to Log4Shell |
| 2021-12-17   15:40 | Vulnerable EC2 instances terminated, because they were development servers         |
| 2021-12-16   ??:00 |                                                                                    |
| 2021-12-14   ??:00 |                                                                                    |
| 2021-12-14   ??:00 |                                                                                    |
| 2021-12-14   ??:00 |                                                                                    |

## Collaboration with other teams

- Difficult to get the message out ot developers about how serious this was.
- Good response from developers and architects when they understood the gravity of the situation.

## Action Items

- Teach the teams how to scan and secure their infrastructure and artifacts (Docker images etc).
- Teach the Product Owners about how to look up the CVEs.
- Figure out how to monitor the critical CVEs.

## Lessons Learned

- Better communication between CE, S&O and IT-Security is needed.
- Tracking which capabilities belonged to which teams (where previous developers had left DFDS, or where the capabilities were old).
- Two teams were occupied with other severity 1 work, which delayed their response to the Log4Shell vulnerability.
- Do we have a good overview of what dependencies our off-the-shelf applications have?
- We need a better way to quickly switch between IAM accounts when doing investigations across accounts.
- No formal definition of a team.
- CE did a lot of scanning and hardening that should have be done by the teams themselves. We did it to speed up the process.
- Some teams were using old infrastructure components. Examples: EC2, ElasticSearch.
- One capability was no longer used, but still had resources on AWS and on Hellman.
- We were not using AWS Inspector to scan EC2 and ECR.

## Attendees

- AN, JS, JW

## Stats

- Category: Cloud Infrastructure and Source Code
- Time to detection: ?
- Time to recovery: ?
