# Node IPC Incident Response

## Summary
We were made aware that a Node.js package that has an update that modified the behaviour in an unintended manner. This package contains malicious code, that targets users with IP located in Russia or Belarus, and overwrites their files with a heart emoji. 
Although this was proven to cause an issue with some companies we were not affected due to not updating to the latest version.

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-23812
https://nvd.nist.gov/vuln/detail/CVE-2022-23812
https://security.snyk.io/vuln/SNYK-JS-NODEIPC-2426370
https://snyk.io/blog/peacenotwar-malicious-npm-node-ipc-package-vulnerability/

## Timeline

All times in UTC.

| Time             | Event                                                                                                                                                                            |
| :--------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2022-03-07 00:00 | Vulnerability committed into node package called node-ipc                                                                                                                        |
| 2022-03-08 00:00 | Fix committed into node-ipc package                                                                                                                                              |
| 2022-03-18 08:37 | Emil notified by social media                                                                                                                                                    |
| 2022-03-18 08:39 | Emil notified team via slack                                                                                                                                                     |
| 2022-03-18 08:59 | Audun looked for the packages in the known repositories, both Github and Azure Dev-Ops                                                                                           |
| 2022-03-18 09:00 | Audun found four effected repositories, two in Azure Dev-Ops, Two in Github                                                                                                      |
| 2022-03-18 09:15 | John evaluated two repositories in Azure Dev-Ops and found one to be production code (MVS UI) with unaffected version and one to be unused code (tech-challenge)                 |
| 2022-03-18 09:20 | John removed "tech-challenge" project as it was a POC                                                                                                                            |
| 2022-03-18 09:25 | Freight team was notified and educated of the issue                                                                                                                              |
| 2022-03-18 10:07 | Audun evaluated two repositories in Github found both repositories able to be archived due to one being sample code (Crossplane-Sandbox) and one not being used (Cost-janitor)   |
| 2022-03-18 10:40 | Ross was assigned for the investigation                                                                                                                                          |
| 2022-03-18 11:20 | Ross made commit to fix the version on unaffected update before package became malicious "f346f834cb453f1c415f7f74bfe7e08e890b26f3" also added context to the commit description |

## Contributing Factors

- Political Situation / War

## Action Items

- We need to have a SCA scanner than can alert us to new supply chain attacks.
- We need a Security Operation Centre (SOC) to help with detecting, managing and mitigating these issues.

## Lessons Learned

- Packages can be highjacked very easy for someone personal gain or political views.
- Difficult to verify transitive dependencies.
- Packages should not always be bumped to the latest version without investigation first.
- Very difficult to validate packages without a tool.
- New package type called "protestware"

## Attendees

- EC, AN, SD, WC, JS

## Stats

- Category: Security Incident
- Time to detection: 11 Days
- Time to recovery: N/A
