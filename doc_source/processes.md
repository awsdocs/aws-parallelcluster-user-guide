# AWS ParallelCluster processes<a name="processes"></a>

This section applies only to HPC clusters that are deployed with one of the supported traditional job schedulers \(SGE, Slurm, or Torque\)\. When used with these schedulers, AWS ParallelCluster manages compute node provisioning and removal by interacting with both the Auto Scaling group and the underlying job scheduler\.

For HPC clusters that are based on AWS Batch, AWS ParallelCluster relies on the capabilities provided by the AWS Batch for the compute node management\.

**Topics**
+ [General overview](#general-overview)
+ [`jobwatcher`](#jobwatcher)
+ [`sqswatcher`](#sqswatcher)
+ [`nodewatcher`](#nodewatcher)
+ [`clustermgtd`](#clustermgtd)
+ [`computemgtd`](#computemgtd)

## General overview<a name="general-overview"></a>

A cluster's lifecycle begins after it is created by a user\. Typically, a cluster is created from the Command Line Interface \(CLI\)\. After it's created, a cluster exists until it's deleted\. AWS ParallelCluster daemons run on the cluster nodes, mainly to manage the HPC cluster elasticity\. The following diagram shows a user workflow and the cluster lifecycle\. The sections that follow describe the AWS ParallelCluster daemons that are used to manage the cluster\.

![\[Cluster lifecycle\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/workflow.png)

## `jobwatcher`<a name="jobwatcher"></a>

When a cluster is running, a process owned by the root user monitors the configured scheduler \(SGE, Slurm, or Torque\)\. Each minute it evaluates the queue in order to decide when to scale up\.

![\[jobwatcher workflow\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/jobwatcher.png)

## `sqswatcher`<a name="sqswatcher"></a>

The `sqswatcher` process monitors for Amazon SQS messages that are sent by Auto Scaling to notify you of state changes within the cluster\. When an instance comes online, it submits an "instance ready" message to Amazon SQS\. This message is picked up by `sqs_watcher`, running on the head node\. These messages are used to notify the queue manager when new instances come online or are terminated, so they can be added or removed from the queue\.

![\[sqswatcher workflow\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/sqswatcher.png)

## `nodewatcher`<a name="nodewatcher"></a>

The `nodewatcher` process runs on each node in the compute fleet\. After the `scaledown_idletime` period, as defined by the user, the instance is terminated\.

![\[nodewatcher workflow\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/nodewatcher.png)

## `clustermgtd`<a name="clustermgtd"></a>

Clusters that run in heterogeneous mode \(indicated by specifying a [`queue_settings`](cluster-definition.md#queue-settings) value\) have a cluster management daemon \(`clustermgtd`\) process that runs on the head node\. These tasks are performed by the cluster management daemon\.
+ Inactive partition clean\-up
+ Static capacity management: make sure static capacity is always up and healthy
+ Sync scheduler with Amazon EC2\.
+ Orphaned instance clean\-up
+ Restore scheduler node status on Amazon EC2 termination that happens outside of the suspend workflow
+ Unhealthy Amazon EC2 instances management \(failing Amazon EC2 health checks\)
+ Scheduled maintenance events management
+ Unhealthy Scheduler nodes management \(failing Scheduler health checks\)

## `computemgtd`<a name="computemgtd"></a>

Clusters that run in heterogeneous mode \(indicated by specifying a [`queue_settings`](cluster-definition.md#queue-settings) value\) have compute management daemon \(`computemgtd`\) processes that run on each of the compute node\. Every five \(5\) minutes, the compute management daemon confirms that the head node can be reached and is healthy\. If five \(5\) minutes pass during which the head node cannot be reached or is not healthy, the compute node is shut down\.