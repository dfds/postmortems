# Kafka metrics stopped being exported to Prometheus

## Summary
Two alerts were triggered indicating the kafka exporter having an outage.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2022-07-07 08:58 | A personal user account deleted from Confluent Cloud |
| 2022-07-07 09:08 | [No Data] Topic has large amount of consumergroups alert triggered |
| 2022-07-07 09:10 | [Alerting] Kafka-exporter up alert triggered |
| 2022-07-07 09:48 | An incident created informing about the outage |
| 2022-07-07 09:55 | Created new API key and attached to service account |
| 2022-07-07 10:00 | Deployed new API key to Hellman |
| 2022-07-07 10:01 | Restarted the kafka-exporter pod |
| 2022-07-07 10:09 | Major outage closed |
| 2022-07-07 12:03 | Reported missing some metrics |
| 2022-07-07 12:05 | Investigation on missing permissions related to the API key started |
| 2022-07-07 13:32 | All needed ACLs were attached to the API key |
| 2022-07-07 13:34 | Metrics are available at the metrics endpoint  |
| 2022-07-07 13:40 | Metrics availability confirmed by end user |

## Contributing Factors

- API key used by kafka exporter belong to a personal account
- No documentation about how to setup ACLs for the API keys
- Limited knowledge about kafka due to holidays

## Action Items

- Service account with ACLs and metricViewer role has been documented

## Lessons Learned

- API keys should owned be by service accounts
- Ensure that any credentials being used in the system must come from centralized secret vault

## Attendees

- AN, SD, MH

## Stats

- Category: Observability
- Time to detection: 10 minutes
- Time to recovery: 4 hours 42 minutes


