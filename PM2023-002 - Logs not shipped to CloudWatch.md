# Logs not being shipped to CloudWatch

## Summary

At noon on Tuesday, eks-pipeline was updated to use the at the time latest release of infrastructure-modules. This meant jumping multiple release versions, which meant multiple changes were introduced at once. One of these changes being upgrading Fluentd to a newer version. Sporadically throughout the day, some nodes stopped shipping logs to CloudWatch. By the time the issue was mitigated, 5 out of 46 nodes were affected.

The issue was mitigated by rolling back to a prior release of Fluentd.

## Timeline
All times in UTC.

| Time             | Event                                                                                                                                |
| :--------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 2023-04-11 11:20 | Fluentd was upgraded to latest version during normal maintenance operations                                                          |
| 2023-04-11 11:20 | First node stops shipping logs to CloudWatch                                                                                         |
| 2023-04-11 17:15 | Second node stops shipping logs to CloudWatch                                                                                        |
| 2023-04-11 17:25 | Third node stops shipping logs to CloudWatch                                                                                         |
| 2023-04-11 18:45 | Fourth node stops shipping logs to CloudWatch                                                                                        |
| 2023-04-11 18:55 | Fifth node stops shipping logs to CloudWatch                                                                                         |
| 2023-04-11 20:28 | Alert for Fluentd is noticed by engineer                                                                                             |
| 2023-04-11 21:01 | After doing some digging, 5 nodes have been discovered missing logs from various times throughout the day                            |
| 2023-04-11 21:06 | 4 options is given for how to proceed                                                                                                |
| 2023-04-11 22:04 | No input has been given for the 4 options. Engineer proceeds with the option to roll back the faulty version to the previous release |
| 2023-04-11 22:17 | Monitoring reports that Fluentd is now fine                                                                                          |
| 2023-04-11 22:23 | Buggy release has been fully reverted back to the previous release                                                                   |
| 2023-04-11 22:25 | The aforemetioned 5 nodes has started shipping logs to CloudWatch again                                                              |

## Contributing Factors

- The way infrastructure-modules is organised (linear releases) makes it tough to stagger releases for eks-pipeline

## Action Items

- Look into liveness probe covering more aspects of the Fluentd process (check /metrics endpoint)
- Investigate if third-party plugins used for Fluentd is maintained
- Agree as a team what the procedures are for dealing with outages after work hours
- Add alarms for Fluentd in case not enough/no log data is being sent (per node)
- Remove hardcoded version variable for Fluentd docker image

## Lessons Learned

-

## Attendees

- RF
- AN
- ED
- SA
- MN
- AF
- EC

## Stats

- Category: Kubernetes
- Time to detection: 544 minutes
- Time to recovery: 669 minutes
