# Logs not shipped to CloudWatch

## Summary

Misconfiguration of fluentd resulted in logs not being shipped to CloudWatch for ninety minutes. Logs in this time span is assumed lost.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2022-01-31 12:00 | New fluentd configuration deployed through Terraform and Flux  |
| 2022-01-31 12:41 | Received alert: Fluentd available buffer space ratio alert |
| 2022-01-31 12:53 | Investigations started |
| 2022-01-31 13:04 | It was discovered the fluentd DeamonSet annotations were using wrong ARN |
| 2022-01-31 13:18 | Change reverted |
| 2022-01-31 13:25 | The fluentd DaemonSets were being restarted with correct ARN |
| 2022-01-31 13:49 | All fluentd DaemonSets were running with correct ARN again |

## Contributing Factors

- Human error in not knowing that Hellman logs are stored in a different AWS account than the cluster is running in.

## Action Items

- TODO

## Lessons Learned

- TODO

## Attendees

- AN

## Stats

- Category: Kubernetes
- Time to detection: 53? minutes
- Time to recovery: 109 minutes
