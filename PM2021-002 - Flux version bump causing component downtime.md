# Flux unintended version update

## Summary

On the 15th of February a scheduled cluster pipeline was automatically triggered, run and failed. An unintentional version update to the GitOps component (Flux) was detected and caused a teardown and re-apply of the component causing the logshipper (fluentd) to be deleted completely from the cluster.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2021-02-15 06:05 | Scheduled cluster deployment pipeline failed, when applying unintentional changes to our GitOps component (Flux), causing the logshipper (fluentd) to be deleted from the cluster. | 
| 2021-02-15 06:45 | Issue detected and verified. |
| 2021-02-15 06:55 | Pipeline attempted rerun. |
| 2021-02-15 07:00 | Everything is back to normal and verified working. |

## Contributing Factors

- No version lock on Flux component
- Version bumps to Flux leads to unwanted downtime
- Lack of testing, evaluation and experience with GitOps and Flux
- Scheduled timeslot for pipeline was run early for historical reasons. But we do not have staff in the office that early anymore. Hence the rather long detection time, but this could have been a lot worse.

## Lessons Learned

- Ensure a better review process to catch things like unlocked versions.
- More risk free testing. Coupling GitOps evaluation with a core production component.
- New tool/component (GitOps) shouldn't have been rushed to production.
- Maintenance in work hours shouldn't rely on a single staff member being present. This setup/idea is simply to fragile.

## Action Items

- Setup more Fluentd alerts
- Flux metrics and alerts
- Change scheduled deployment pipeline timeslot
- Version lock Flux

## Stats

- Category: Observability
- Time to detection: 40 minutes
- Time to recovery: 55 minutes
