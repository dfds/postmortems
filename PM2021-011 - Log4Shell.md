# Log4Shell investigations

## Summary

Investigating if DFDS was using applications or cloud services that depended on Log4J. If anything was found we needed to assess if these applications and services were affected by the Log4Shell vulnerability.

CVE details: <https://nvd.nist.gov/vuln/detail/CVE-2021-44228>

## Timeline

All times in UTC.

| Time               | Event                                                                                                                                                                    |
| :----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 2021-12-10         | The Log4Shell vulnerability was published                                                                                                                                |
| 2021-12-10   15:40 | We became aware of it during a call with Peter Taylor                                                                                                                    |
| 2021-12-10   16:10 | IT-Security published messaged on MS Teams and Slack about it                                                                                                            |
| 2021-12-10   16:10 | Azure DevOps git repositories were searched for usage of Log4J                                                                                                           |
| 2021-12-10   19:00 | People in CE was discussing internally about Log4J, and decided to advice S&amp;O                                                                                        |
| 2021-12-13   09:15 | Started investigating DFDS' usage of ElasticSearch/OpenSearch on AWS                                                                                                     |
| 2021-12-13   11:45 | Jan and John discussing a collaboration with S&amp;O                                                                                                                     |
| 2021-12-13   15:50 | Started scanning Docker images used on Hellman. One affected pod found (xroute)                                                                                          |
| 2021-12-13   14:30 | Initial management-level contact with S&amp;O                                                                                                                            |
| 2021-12-13   16:29 | (Lloyd Middleton) Common task coordination board created                                                                                                                 |
| 2021-12-14   09:15 | Scanning all images in ECRs not covered by the first Docker layer scan                                                                                                   |
| 2021-12-14   10:50 | Formal notification to EA group                                                                                                                                          |
| 2021-12-14   11:11 | Communication with the architects started                                                                                                                                |
| 2021-12-14   11:30 | (T&amp;I level) Initial meeting of all direct involved and incident manager (Søren Iversen) appointed                                                                    |
| 2021-12-16   14:00 | Scanning EC2 instances started                                                                                                                                           |
| 2021-12-16   16:14 | Update requested on cloud infrastructure progress                                                                                                                        |
| 2021-12-16   17:18 | Update on cloud infrastructure progress given to Soren, Lloyd and Peter Taylor                                                                                           |
| 2021-12-17   12:00 | Four EC2 servers found with critical vulnerabilities, but not related to Log4Shell, teams notified                                                                       |
| 2021-12-17   12:45 | AWS Lambdas checked. Four Java based Lambdas were found. They were not checked for Log4J. Agreed with developer to delete them since they were just for testing purposes |
| 2021-12-17   13:00 | AWS Glue checked, and no vulnerabilities found                                                                                                                           |
| 2021-12-17   14:00 | 2nd call for self-reporting from developers - no responses received                                                                                                      |
| 2021-12-17   14:40 | Vulnerable EC2 instances terminated, because they were development servers                                                                                               |
| 2021-12-17         | log4j 2.17 published                                                                                                                                                     |
| 2021-12-18         | CE evaluated that no action was needed regarding new 2.17 release. Verified that AWS had autopatched where applicable.                                                   |
| 2021-12-20   09:15 | AWS SageMaker checked, and no vulnerabilities found.                                                                                                                     |
| 2022-01-04 11:03   | Follow-up on missing feedback from Logistics x-route team. Product was patched and no further action was required.                                                       |

## Action Items

- Teach the teams how to scan and secure their infrastructure and artifacts (Docker images etc)
- Teach the Product Owners about how to look up the CVEs
- Figure out how to monitor the critical CVEs
- Automatic process to detect CVE in products and be notified when the CVE database is updated
- Setup an automated inventory system for AWS resources (for example <https://github.com/darkbitio/aws-recon>)
- Create a process for identifying the missing capability owners.

## Lessons Learned

### Reflections on external collaboration

- With S&O
  - Initial coordination unstructured but rapidly improved
  - Shared board worked well to get an overview although there was little cross-group assistance
  - Mitigation information sharing was unstructured. Some suggestions mentioned in full group video meetings but not written down, others in DMs between individuals.
- Difficult to get the message out to developers about how serious this was
  - No clear communication channel to developers and PO's
  - Initial Friday post on Teams easily missed but in theory readable by all of T&I. Also posted out of office hours
  - Follow-up message on Slack posted in channel with 160-170 members only. 6 people acknowledged seeing it
- Good response from developers and architects when they understood the gravity of the situation
  - Feedback (on resolution) was occasionally slow

### General

- (Process/Structure) Better communication between CE, S&O and IT-Security is needed
- (Process/Structure) Tracking which capabilities belonged to which teams (where previous developers had left DFDS, or where the capabilities were old)
- Two teams were occupied with other severity 1 work, which delayed their response to the Log4Shell vulnerability
- Do we have a good overview of what dependencies our off-the-shelf applications have?
- We need a better way to quickly switch between IAM accounts when doing investigations across accounts
- (Process/Structure) No formal definition of a team which creates communication friction
- (Process/Structure) CE did a lot of scanning. We also manually upgraded a few affected ElasticSearch instances. The latter could have be done by the teams themselves. We did it to speed up the process
- Only 1 team reacted proactively to the vulnerability
- Some teams were using old infrastructure components. Examples: EC2, ElasticSearch
- One capability was no longer used, but still had resources on AWS and on Hellman
- We were not using AWS Inspector to scan EC2 and ECR
- An automatically updated inventory for AWS resources would decrease friction during investigations. We have scripts for the purpose but they take time to run and results are only immediately available to the person running the script

## Attendees

- AN, JS, JW

## Stats

- Category: Cloud Infrastructure and Source Code
- Time to detection: N/A
- Time to recovery: N/A
