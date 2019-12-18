# Hellman Kubernetes cluster node deletion

## Summary

On Wednesday 4 December 2019, an accidental execution of a Kubernetes command deleted all the worker nodes from Kubernetes cluster state. This resulted in a period of 30 minutes where jobs could not be scheduled in the cluster from a lack of registered worker nodes.

A lack of Priority-classes and the IP starvation issue in the cluster resulted in further complications. After the cluster in effect was up and operational, the lack of priority for service critical workloads such as KIAM resulted in some workloads not being operational even though the pods themselves where up and running.

## Timeline

| Date and Time in CET | Event                                                                                                                                                                                               |
|:--------------------:|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   2019-12-04 00:30   | Accidental execution of command that deleted all worker nodes in Hellman Kubernetes cluster, resulting in workloads being unschedulable                                                             |
|   2019-12-04 00:35   | Manually marked VMs as unhealthy, triggering automatic provisioning of new instances to join the Kubernetes cluster                                                                                 |
|   2019-12-04 01:00   | Verified that new nodes joined cluster and most deployments are up and running again                                                                                                                |
|   2019-12-04 01:05   | Some critical daemonsets (i.e. KIAM) not operational, due to IP address starvation these workloads could not be scheduled on all nodes as they should be (see post-mortem [PM2019-009][PM2019-009]) |
|   2019-12-04 01:15   | Applying built-in priority-classes to schedule critical components before other workload, ensuring operations of critical workloads                                                                 |
|   2019-12-04 01:30   | Verified all systems operational                                                                                                                                                                    |

## Contributing Factors

While we consider the lack of **Least-privilege in Hellman Kubernetes cluster** implemented in our day to day operations the biggest factor in this incident, there were a number of contributing factors that enhanced the effects of this incident:

- IP address starvation in Hellman network infrastructure (see post-mortem [PM2019-009][PM2019-009]). This resource restraint resulted in scheduled pods crowding nodes when they became available, leaving no room for other system components.

- Priority-classes not implemented on system critical workloads, resulting in a scenario where other workloads could crowd the nodes leaving no capacity left for the system critical workloads.

## Lessons Learned

From this incident we learned the risk and impact of using higher privileges than necessary for day to day operations in the kubernetes cluster. It should not be possible to perform high impact destructive operations like deleting nodes from the cluster without explicitly electing a context which allows such operations.

While re-provisioning the nodes in the cluster, a previously unfelt issue arose when system critical workloads where not scheduled on the new nodes due to resource starvation in the form of IP address limitations. While we should ideally never be in a situation where the cluster is in a resource starved state, it is imperative that should such a state be reached, then system critical workloads should have priority to allow operations running as smoothly as possible in such circumstances. 

## Action Items

- **Implement least-privilege in Hellman Kubernetes cluster**: We are working on defining different contexts to be used for different types of operations and ensuring that admins have access to cluster configurations that matches the required contexts.

- **Implement priority-classes for critical workloads**: Has been scheduled as part of our effort to improve resilience in the cluster and ensuring that resource restrictions does not create potential for a node missing a critical component.

[PM2019-009]: https://github.com/dfds/postmortems/blob/master/PM2019-009%20-%20Kubernetes%20network%20infrastructure%20IP%20starvation.md