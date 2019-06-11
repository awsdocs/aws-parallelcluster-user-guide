# AWS ParallelCluster Processes<a name="processes"></a>

This section applies only to HPC clusters that are deployed with one of the supported traditional job schedulers \(SGE, Slurm, or Torque\)\. When used with these schedulers, AWS ParallelCluster manages the compute node provisioning and removal by interacting with both the Auto Scaling Group \(ASG\) and the underlying job scheduler\.

For HPC clusters that are based on AWS Batch, AWS ParallelCluster relies on the capabilities provided by the AWS Batch for the compute node management\.

**Topics**
+ [General Overview](#general-overview)
+ [`jobwatcher`](#jobwatcher)
+ [`sqswatcher`](#sqswatcher)
+ [`nodewatcher`](#nodewatcher)

## General Overview<a name="general-overview"></a>

A cluster's lifecycle begins after it is created by a user\. Typically, a cluster is created from the Command Line Interface \(CLI\)\. After it's created, a cluster exists until it's deleted\. AWS ParallelCluster daemons run on the cluster nodes, mainly to manage the HPC cluster elasticity\. The following diagram shows a user workflow and the cluster lifecycle\. The sections that follow describe the AWS ParallelCluster daemons that are used to manage the cluster\.

![\[Cluster lifecycle\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/workflow.png)

## `jobwatcher`<a name="jobwatcher"></a>

When a cluster is running, a process owned by the root user monitors the configured scheduler \(SGE, Slurm, or Torque\) and each minute, it evaluates the queue in order to decide when to scale up\.

![\[jobwatcher workflow\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/jobwatcher.png)

## `sqswatcher`<a name="sqswatcher"></a>

The `sqswatcher` process monitors for Amazon SQS messages that are sent by Auto Scaling, to notify you of state changes within the cluster\. When an instance comes online, it submits an "instance ready" message to Amazon SQS\. This message is picked up by `sqs_watcher`, running on the master node\. These messages are used to notify the queue manager when new instances come online or are terminated, so they can be added or removed from the queue\.

![\[sqswatcher workflow\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/sqswatcher.png)

## `nodewatcher`<a name="nodewatcher"></a>

The `nodewatcher` process runs on each node in the compute fleet\. After the `scaledown_idletime` period, as defined by the user, the instance is terminated\.

![\[nodewatcher workflow\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/nodewatcher.png)