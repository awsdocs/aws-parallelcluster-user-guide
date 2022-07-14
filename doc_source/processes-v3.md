# AWS ParallelCluster processes<a name="processes-v3"></a>

This section applies to clusters that are deployed with Slurm\. When used with this scheduler, AWS ParallelCluster manages compute node provisioning and removal by interacting with the underlying job scheduler\.

For HPC clusters that are based on AWS Batch, AWS ParallelCluster relies on the capabilities provided by the AWS Batch for the compute node management\.

## `clustermgtd`<a name="clustermgtd-v3"></a>

The following tasks are performed by the cluster management daemon\.
+ Inactive partition clean\-up
+ Static capacity management: make sure static capacity is always up and healthy
+ Sync scheduler with Amazon EC2\.
+ Orphaned instance clean\-up
+ Restore scheduler node status on Amazon EC2 termination that happens outside of the suspend workflow
+ Unhealthy Amazon EC2 instances management \(failing Amazon EC2 health checks\)
+ Scheduled maintenance events management
+ Unhealthy Scheduler nodes management \(failing Scheduler health checks\)

## `clusterstatusmgtd`<a name="clusterstatusmgtd-v3"></a>

Cluster status management daemon is in charge to manage the compute fleet status update. Every minute it fetches the fleet status stored in a DynamoDB table and manages any STOP/START request.  

## `computemgtd`<a name="computemgtd-v3"></a>

Compute management daemon \(`computemgtd`\) processes run on each of the cluster compute nodes\. Every five \(5\) minutes, the compute management daemon confirms that the head node can be reached and is healthy\. If five \(5\) minutes pass during which the head node cannot be reached or is not healthy, the compute node is shut down\.