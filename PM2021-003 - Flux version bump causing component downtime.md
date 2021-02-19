# Flux unintended version update

## Summary

On the 19th of February a scheduled cluster pipeline was automatically triggered, run and failed. An unintentional version update to the GitOps component (Flux) was detected and caused a teardown and re-apply of the component causing the logshipper (fluentd) to be deleted completely from the cluster.

## Timeline

All times in CET.

| Time | Event |
| :--- | --- |
| 2021-02-19 08:35 | Scheduled cluster deployment pipeline failed, when applying unintentional changes to our GitOps component (Flux), causing the logshipper (fluentd) to be deleted from the cluster. | 
| 2021-02-19 08:55 | Issue detected and verified. |
| 2021-02-19 08:58 | Pipeline attempted rerun. |
| 2021-02-19 09:08 | Second attempt at rerunning the pipeline. |
| 2021-02-19 09:16 | Applied manual fix to remove finalizers from Flux customresourcedefinition. |
| 2021-02-19 09:18 | Reran pipeline to kick Flux back in. |
| 2021-02-19 09:23 | Everything is back to normal and verified working. |

## Contributing Factors

- No version lock on Flux component
- Version bumps to Flux leads to unwanted downtime
- Lack of testing, evaluation and experience with GitOps and Flux
- Did not disable pipeline despite being aware of the risk

## Lessons Learned

- If we are aware of a risk, however unlikely we think, we should act on it.
- Ensure a better review process to catch things like unlocked versions.
- More risk free testing. Coupling GitOps evaluation with a core production component.
- New tool/component (GitOps) shouldn't have been rushed to production.
- Maintenance in work hours shouldn't rely on a single staff member being present. This setup/idea is simply to fragile.

## Action Items

- Flux metrics and alerts
- Version lock Flux
- Disable pipeline until issue fixed

## Attendees

- rifis
- wicar

## Stats

- Category: Observability
- Time to detection: 20 minutes
- Time to recovery: 48 minutes
