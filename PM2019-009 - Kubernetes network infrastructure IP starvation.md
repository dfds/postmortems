# Kubernetes network infrastructure IP starvation

## Summary

On Friday 29 November 2019, we proactively scaled the amounts of worker nodes in the cluster to increase capacity of possible deployments to be scheduled, in anticipation of teams migrating from our old Prod and NonProd clusters to the new Hellman cluster. This increase would turn out to push the network architecture and design of our cluster to the limit.

**The underlying issue**:  
A quick TL;DR of the underlying technical issue.
Our cluster runs in different availability zones in Amazons datacenters, giving us resiliency if a single availability zone becomes unoperational or has service disruptions.
Each availability zone has its own subnet, and runs its own set of worker nodes. The subnet in each zone is a /24 giving us 250 usable IP addresses for server workloads.

In most non AWS specific Kubernetes deployments, only the worker nodes themselves would consume these IP addresses, allowing a /24 to potentially have 250 worker nodes. However, in EKS Amazon implements their own Container Networking Interface (CNI) inside of their Kubernetes service. This CNI design consumes an IP per kubernetes pod deployed in the cluster. This means that not only does scaling the amount of worker nodes to increase resources in the cluster consume IP's, each replica of each deployment will also consume an IP.

This design created a situation where our network architecture and design would need to balance between the amount of nodes in each zone and the amount of deployments per zone.

**What happened?**:  
On Saturday 30 November 2019, we got an automated warning from AWS, informing us that we where nearing the limit in some subnets and our load balancers were unable to scale as a result.
We immediately begin working on a solution to redesign the underlying network architecture to allow for better scaling in each availability zone.

A balancing act ensued, scaling the amounts of nodes in the cluster to free IP addresses while maintaining enough resources to run workloads.

On Thursday 05 December 2019, we started a process where we changed one availability zone at a time and migrated the workloads from nodes in the old subnets to nodes in the new one.
Other than some minor challenges which arose from the IP starvation, the transition went smoothly. Some of the changes to the infrastructure where done manually and thus a clean-up of our IaC state would need to be done afterwards.

## Timeline

| Date and Time in CET | Event                                                                                                                                                                      |
|:--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   2019-11-29 12:00   | Proactively scaling up Hellman Kubernetes cluster from 7 to 10 nodes, to ensure sufficient capacity for team migrations                                                    |
|   2019-11-30 08:24   | Automated warning from AWS, that the load balancer for Hellman Kubernetes ingress, were *unable to scale up because there are not enough free IP addresses in its subnets* |
|   2019-11-30 09:00   | Started working on a long-term solution                                                                                                                                    |
|   2019-11-30 10:30   | Scaling down cluster by 2 nodes (to 8) to free up IP addresses                                                                                                             |
|   2019-12-02 04:30   | Scaling down cluster by 1 node (to 7) to free up IP addresses                                                                                                              |
|   2019-12-03 --:--   | Surge new deployments increasing the amount of pods (100+) rendering the circumstances exigent                                                                             |
|   2019-12-04 03:00   | Solution completed, handed over to team in DK for testing and verification                                                                                                 |
|   2019-12-04 09:30   | Asking teams to temporarily scale down deployments to free up IP addresses                                                                                                 |
|   2019-12-05 03:30   | Started replacing Hellman Kubernetes network infrastructure and scaling up cluster to 12 nodes                                                                             |
|   2019-12-05 05:30   | Forcefully killing nodes to free up IP addresses, as we were now running so low, we were not able to reconfigure load balancer                                             |
|   2019-12-05 06:00   | Network infrastructure replacement completed                                                                                                                               |
|   2019-12-05 06:10   | Some Kubernetes daemon sets not scheduled, due to pod scheduling storm                                                                                                     |
|   2019-12-05 07:00   | All systems verified operational                                                                                                                                           |
|   2019-12-05 12:00   | Pull request to review changes unexpectedly triggers Hellman Kubernetes cluster pipeline, ungracefully reverts changes to infrastructure (see separate post-mortem)        |

## Contributing Factors

- No monitoring of network subnet capacity let to a known limitation in the architecture become an acute problem.
- Lack of revision of deployed infrastructure, due to heavy focus on developing new features allowed the issue to escalate further.
- Not possible to add availability zones to existing Kubernetes EKS clusters, complicating a long-term solution to IP starvation.
- Not possible to update load balancer configuration (to fix the problem), due to near 100% IP address utilization in subnets.
- One single Kubernetes node autoscaling group across AWS availability zones limiting flexibility.
- We have announced sunset dates for other Kubernetes clusters.
- Teams have recently been encouraged teams to migrate to Hellman.

## Lessons Learned

From this incident we learned that our way of monitoring and handling our approach to lower level infrastructure is problematic.
Focusing on adding value and new features and fixing problems when you are working on that part of your stack to add value is not a good fit for all classic infrastructure challenges.
This let to an issue which should have been addressed before it created any impact become a pressing concern.

## Action Items

- **Make sure all deployed resources have a plan for monitoring / scaling**: This is a work in progress and we will start revising all our components and evaluate where we are vulnerable or in the dark. 

- **Separate Kubernetes node autoscaling groups per AWS availability zone**: Will allow more organic scheduling of workloads as Kubernetes is not aware of our availability zones, splitting these up can help prevent a range of scheduling issues.

- **Implement priority-classes for critical workloads**: It can happen even in none resource starved states that critical workloads that needs to be on every node cannot be scheduled if that node is maxed out, priority-classes will ensure this never happens.

- **Consider separate prioritization of SRE tasks**: SRE's in the team need to have a better day to day operations feeling of what is going on in the lower levels of our infrastructure, as such we need to ensure a way tasks concering reliability are prioritized properly.

