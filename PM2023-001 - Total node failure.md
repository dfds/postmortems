# Total node failure

## Summary

Due to Kubernetes logs not being shipped to CloudWatch, an unscheduled node rollover was triggered to remedy that. The node rollover attempt timed out(faulty configuration), and a subsequent rollover attempt to fix that killed the healthy worker nodes because of two reasons. The faulty nodes weren't removed yet and the default termination policy is to remove nodes with the oldest launch template at the time. This meant that the cluster was left without any nodes. Multiple node rollover attempts later, the cluster was back in a working state with the expected amount of worker nodes.

## Timeline

All times in UTC.

| Time             | Event                                                                                                                                                                                                                                                                                                          |
| :--------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2023-02-13 07:30 | Scheduled node rollover initiated and containerd had been set as the default container runtime accidentally                                                                                                                                                                                                    |
| 2023-02-14 09:33 | End-users alerted us that CloudWatch logs weren't working as expected. This triggered an investigation into fluentd, that eventually lead us onto the container-runtime being containerd instead of docker-shim                                                                                                |
| 2023-02-14 10:29 | Node rollover started (container-runtime swapped back to what was at the time assumed to be the correct default value for docker-shim in the eks-bootstrap script)                                                                                                                                             |
| 2023-02-14 10:39 | Node rollover attempt fails due to timeout (container-runtime misconfiguration meant the new nodes never joined the cluster). New nodes becomes "zombies".                                                                                                                                                     |
| 2023-02-14 10:45 | EKS-Pipeline without node rollover attempt is being run. This applies the new launch template with dockerd being set as the container runtime. Since the previous node rollover attempt failed, the desired ASG size wasn't never changed back. Terraform detects this, setting desired ASG size from 30 to 15 |
| 2023-02-14 10:46 | Healthy nodes all gets killed. This is due to the ASG desired size being set back to 15, and the default termination policy is to get rid of the instances using the oldest launch config/template                                                                                                             |
| 2023-02-14 10:48 | We have no nodes                                                                                                                                                                                                                                                                                               |
| 2023-02-14 10:49 | Attempt at running node rollover script to add new nodes                                                                                                                                                                                                                                                       |
| 2023-02-14 10:51 | Attempt fails due to timeout from Docker Hub                                                                                                                                                                                                                                                                   |
| 2023-02-14 10:52 | 2nd attempt at running node rollover script to add new nodes                                                                                                                                                                                                                                                   |
| 2023-02-14 10:57 | New nodes are being added                                                                                                                                                                                                                                                                                      |
| 2023-02-14 11:01 | LB detects new nodes as healthy, allowing traffic                                                                                                                                                                                                                                                              |
| 2023-02-14 11:07 | Sev 1 ticket raised                                                                                                                                                                                                                                                                                            |
| 2023-02-14 11:59 | Sev 1 ticket marked as resolved                                                                                                                                                                                                                                                                                |


## Contributing Factors

- QA config differs from prod, missing staging environment
- QA test suite doesn't cover fluentd log collection
- Rollover process has no documentation in place for what happens if it goes bad
- During the attempt to revert the container-runtime, the value was thought to be dockershim, but was instead dockerd

## Action Items

- Document rollover process. Especially what can be done in case it goes bad
- Investigate options for improving rollover process
- Add coverage for fluentd to QA test suite. Ensure that logs from A to Z is covered
- Add alarms for fluentd in case not enough/no log data is being sent
- Implement staging environment

## Lessons Learned

- In case the situation turns critical, remain calm and collected. It's better not to rush a fix and potentially make the damage worse
- Instead of trying to resolve the issue, just roll it back to a known working configuration
- Launching new worker nodes rely heavily on the cloudinit script not failing in any way

## Attendees

- AN
- ED
- RN
- SA
- EC

## Stats

- Category: Kubernetes
- Time to detection: 2 minutes
- Time to recovery: 13 minutes
