# Hellman Kubernetes IaC accidentally reverted

## Summary

On Thursday 5 December 2019, we changed a lot of the underlying infrastructure in the Kubernetes cluster Hellman (see postmorten [PM2019-009][PM2019-009] for more details).
Most changes done where a part of our Infrastructure as Code (IaC) state which describes our infrastructure, however, some changes which where part of our migration between the old and new subnets in the different availability zones where done manually. All these changes where preformed by our SRE team in the US.

These manual changes where then supposed to aligned in our IaC later the same day by our team in Denmark, however when to old settings where committed to a branch and a pull request where readied, the IaC pipeline was unintentionally triggered.

The trigger was a result of the default settings in our pipeline provider Azure DevOps. If you don't explicitly turn it off, any pull request will trigger the pipeline to run. This makes a lot of sense if your repository contains code you wish to compile and test before merging with a branch, but it is not ideal for IaC as any execution will change the live state of your systems.

This unintentional execution of the pipeline reverted all changes made by the US team during the night, putting the cluster back into the same bad state as described in the [PM2019-009][PM2019-009] postmortem.

The change was quickly noticed by the team in Denmark and a process update the state to the correct new one was expedited to fix the predicament. 

## Timeline

| Date and Time in CET | Event                                                                                                                                    |
|:---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2019-12-05 04:00     | Team in US manually executed Terraform to update Kubernetes network infrastructure                                                       |
| 2019-12-05 07:00     | Coordinate and hand-over code changes and clean-up procedure between team in US and DK                                                   |
| 2019-12-05 10:00     | Team in DK reviews and revise handed-over code and procedure                                                                             |
| 2019-12-05 12:00     | Pull request to review changes unexpectedly triggers Hellman Kubernetes cluster pipeline, ungracefully reverts changes to infrastructure |
| 2019-12-05 12:05     | Pull request updated with new infrastructure code                                                                                        |
| 2019-12-05 12:10     | Corrective action delayed due to Terraform state lock                                                                                    |
| 2019-12-05 12:15     | State lock forcefully cleared                                                                                                            |
| 2019-12-05 12:25     | Hellman Kubernetes cluster pipeline re-queued and finishes successfully                                                                  |
| 2019-12-05 12:30     | Some Kubernetes daemon sets not scheduled, due to pod scheduling storm                                                                   |
| 2019-12-05 12:35     | Pods manually deleted on fully scheduled nodes, enabling daemonsets to be scheduled                                                      |
| 2019-12-05 12:40     | All systems verified operational                                                                                                         |

## Contributing Factors

- Priority-classes not implemented
- Kubernetes pipeline unexpectedly triggered
- Terraform state locked, while no operation was taking place. Remediation steps unclear.

## Lessons Learned

From this incident we learned that not having a clear way all IaC pipelines are structured can result in unexpected behaviors which can lead to unintentional state of infrastructure. Since repositories and pipelines are created and configured very individually with no common guidelines for contribution between them, behavior can vary. Implementing a common framework for contribution and CI/CD should go a long way to help resolve this pain point. 

## Action Items

- **Implement priority-classes for critical workloads**: It can happen even in none resource starved states that critical workloads that needs to be on every node cannot be scheduled if that node is maxed out, priority-classes will ensure this never happens.

- **Implement rules and guidelines for repository contribution**: Each repository should have clear rules, guidelines and enforced policies in regards to contribution of changes to master branch, based on a standardized framework across repositories.

- **Implement standardized pipeline configurations**: Each repository should have a clear and standardized baseline for CI/CD flow of IaC code to help ensure no executions of code is ever run outside the intended context.

- **Run `terraform plan` in CI step to test commits**: A `terraform plan` command should be run in the CI part of every IaC pipeline to validate the changes to the infrastructure before the changes are committed to CD, making consequence of applying visible and clear.

- **Document procedure for breaking Terraform state locks**: In case of unexpected locking of the IaC terraform state, documentation for breaking the statelock should be easily accessible to quickly remedy the situation and expedite any fix/solution.


[PM2019-009]: https://github.com/dfds/postmortems/blob/master/PM2019-009%20-%20Kubernetes%20network%20infrastructure%20IP%20starvation.md