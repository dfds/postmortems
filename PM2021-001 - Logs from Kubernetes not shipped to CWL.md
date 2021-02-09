# Logs from Kubernetes worklaods not being shipped to CloudWatch Logs

## Summary

On 5. January we deployed a new CSI driver to our Hellman Kubernetes cluster. To support this the annotations of the `kube-system` namespace, now managed by Infrastructure-as-Code (or IaC), was modified, accidentally overwriting existing annotations. The overwritten annotations were necessary to allow FluentD, our logshippper, to ship logs to CloudWatch Logs (or CWL).

Once we were made aware of the issue (thanks, Niels Lunn), we manually patched the annotations to restore log shipment. We then proceeded to modify our IaC modules to handle any additional annotations, before applying a long-term fix through our pipeline.

## Timeline

All times in CET.

| Time             | Event                                                                                                                                                            |
| :--------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2021-01-05 11:25 | A new CSI driver was deployed to Hellman Kubernetes, along with changes to the `kube-system` namespace, breaking required annotations for our logshipper FluentD |
| 2021-01-06 11:00 | Developer notified us of potential issue with logging                                                                                                            |
| 2021-01-06 11:20 | Manual fix applied                                                                                                                                               |
| 2021-01-06 15:26 | Long-term fix applied via IaC and pipeline                                                                                                                       |

## Contributing Factors

- FluentD typically not running on operators' Kubernetes sandboxes or in our automated QA
- `kube-system` namespace not managed by Terraform
- Workloads deployed into the `kube-system` namespace, that can or should be deployed to another namespace
- We have a dashboard that clearly showed the problem, but no alerts setup for abnormal behaviour

## Lessons Learned

Historically we have used the `kube-system` namespace to deploy any workloads, that provide a shared service in the cluster. We need to be better at deploying unrelated workloads into separate namespaces.

FluentD is not part of our Kubernetes pipeline, and has typically not been deployed in our sandbox or QA environments. We need an easy way of deploying FluentD (and other essential cluster workloads) to all our environments - production, QA and sandboxes. E.g. GitOps.

When creating dashboards, think about what represents abnormal behaviour and create alerts based on that.

## Action Items

- Setup alerts for when logs are not shipped to CWL - consider monitoring both receiving and sending part (issue created)
- Discuss the feasibility of using GitOps for essential cluster workloads (Rasmus call for meeting)
- Discuss modularity of our `infrastructure-modules` Terraform repo (Willi call for meeting)

## Stats

- Category: Observability
- Time to detection: 1415 minutes
- Time to recovery: 1435 minutes
