# Kafka metrics missing from Prometheus

## Summary

Kafka metrics missing from Prometheus due to kafka-exporter's /metrics endpoint being too slow to load.

## Timeline

All times in UTC.

| Time             | Event                   |
| :--------------- | ----------------------- |
| 2022-02-08 12:20 | kafka-exporter's /metrics load time gets greater than Prometheus scrape timeout (10s).  |
| 2022-02-09 06:16 | Developer asks if Prometheus is up. |
| 2022-02-09 08:03 | **Discovery:** Developer explains that he sees no Kafka metrics in Grafana. |
| 2022-02-09 08:27 | We discover that Prometheus consider kafka-exporter down due to the 10s scrape timeout. |
| 2022-02-09 08:40 | **Recovery:** Prometheus scrape timeout raised to 30 seconds as a quick fix. |
| 2022-02-09 08:45 | Permanent fix added as Infrastructure as Code. |
| 2022-02-09 09:08 | Root cause analysis showed that one capability created a large amount of unique Kafka consumergroups. |
| 2022-02-09 09:25 | Contacted the capability members about their design choice and its impact. Advised them about alternative solution. |
| 2022-02-09 13:08 | Upgraded the kafka-exporter to gain minor performance increase.  |
| 2022-02-?? ??:?? | TBA: Problem will remain until the capability has reworked their consumergroup usage.  |

## Contributing Factors

- Low knowledge about how to reset the topic's offset for consumergroups.
- We did not receve alerts on kafka-exporter' /metrics endpoint response times.
- Ongoing kube-prometheus-stack upgrade masked the issue.

## Action Items

- Check why did not get alert of kafka-exporter response times. An issue has been created for this.
- Consider to setup an alert if a Kafka topic has a high number of consumergroups. (What 'high' is TBD).

## Lessons Learned

- Consider that each capability that wants Kafka metrics should deploy their own instance of kafka-exporter, since there is currently no guardrails.

## Attendees

- AN
- EC
- WC

## Stats

- Category: Observability
- Time to detection: 19 hours 43 minutes.
- Time to recovery:  20 hours 20 minutes.
