# Kube state metrics exposing wrong labels

## Summary

Following the merge of PR #1237 which migrated exporters and service monitors from kube-prom stack to k8s-monitoring deployment, some alerts were being triggered after the change. Reason was the configuration change on the labels exposed by the kube state metrics, as a result on our efforts to migrate to Grafana Cloud from our legacy Prometheus setup.
A fix was ready but the change was not possible to roll out immediately due to a blocking change in the infrastructure pipeline that was not ready to be rolled out in production.
These alarms had to be silenced until the next day when the fix was ready to be implemented.

## Timeline

All times in UTC.

| Time             | Event                                                                                                                        |
| :--------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| 2024-06-06 11:27 | User reported wrong labels on kube-state metrics
| 2024-06-06 12:41 | Issue root cause identified and a fix has been planned
| 2024-06-06 14:10 | Fix has been attempted to be rolled out in production but has been blocked by other changes introduced in the terraform modules that was not was not stable enough to work in production
| 2024-06-06 14:20 | Unstable changes attempted to be rolled back but it was not possible to get them rolled back with confidence so a change
| 2024-06-06 16:07 | Incident is created to notify users on the impact and steps to minimize the noise generated alerts triggered by missing labels on the metrics along with expectation to when solution is rolled out.
| 2024-06-07 08:33 | All components were back operation and no major issues were identified. Teams were notified on #dev-peer-support
| 2024-06-06 09:41 | We've been notified that some metrics no longer are displayed in their Grafana Dashboards as a result of the migration.

## Contributing Factors

- A migration work on monitoring setup
- Drift between infrastructure configuration and production state
- Pushing changes that are known to be unstable that may block production changes

## Action Items

- Avoid drift by ensuring production state reflects as much as possible the configuration changes (no more than a release behind with minor changes)
- Unstable changes should always be in branch and can be tested in staging if needed

## Lessons Learned

## Attendees

SC, SD

## Stats

- Category: Prometheus
- Time to detection: 30 minutes
- Time to recovery: 22 hours
