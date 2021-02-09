# KIAM Service disruption

## Summary

In the afternoon of January 21st, the server certificate for KIAM - a service that assumes AWS IAM roles from pods - expired, causing its service to be disrupted.

This in turn resulted in access to AWS resources - from Kubernetes pods - that use the IAM role to fail.

## Timeline

All times in CET.

| Time             | Event                                                                          |
| :--------------- | ------------------------------------------------------------------------------ |
| 2020-01-21 15:36 | Certificate for KIAM server expires                                            |
| 2020-01-21 16:02 | First alert indicating a problem, but from an affected service (`k8s-janitor`) |
| 2020-01-21 16:30 | First alert regarding KIAM server pods crash-looping                           |
| 2020-01-21 18:04 | New certificates generated, service restored                                   |

## Contributing Factors

- Insufficient monitoring of the KIAM server
- Too much noise in our alert Slack channel, drowning important alerts
- We had no documentation or notes on how to create/update certificates

## Lessons Learned

We need to be more aware when deploying resources depending on certificates, or other objects, that have an expiration date. A pragmatic option would be to create a backlog item or a reminder when deploying. Even better, where possible: Monitor the expiration directly.

Furthermore, when a deployment depends on non-trivial processes, these need to be documented.

## Action Items

- Document the process of creating/updating KIAM certificates
- We have added a backlog task to curate for which scenarios we want to receive alerts
- Replace KIAM server with "EKS Service IAM Role"

# Stats

- Category: Kubernetes
- Time to detection: 54 minutes
- Time to recovery: 148 minutes
