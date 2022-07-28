# Slurm cluster fast insufficient capacity fail\-over<a name="slurm-short-capacity-fail-mode-v3"></a>

Starting with AWS ParallelCluster version 3\.2\.0, clusters run with the fast insufficient capacity fail\-over mode enabled by default\. This minimizes the time spent retrying to queue a job when EC2 insufficient capacity errors are detected\. This is particularly effective when you configure your cluster with multiple kinds of instance types\.

**EC2 detected insufficient capacity failures:**
+ `InsufficientInstanceCapacity`
+ `InsufficientHostCapacity`
+ `InsufficientReservedInstanceCapacity`
+ `MaxSpotInstanceCountExceeded`
+ `SpotMaxPriceTooLow`: Activated if the Spot request price is lower than the minimum required Spot request fulfillment price\.
+ `Unsupported`: Activated with the use of an instance type that isn't supported in a specific AWS Region\.

In fast insufficient capacity failure\-over mode, if an insufficient capacity error is detected when a job is assigned to a [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`compute resource`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-ComputeResources), AWS ParallelCluster:

1. Sets the compute resource to a disabled \(`DOWN`\) state for a predefined period of time\.

1. Uses `POWER_DOWN_FORCE` to cancel the compute resource failing node jobs and to suspend the failing node\. It sets the failing node to the `IDLE` and `POWER_DOWN (!)` state, and then to `POWERING_DOWN (%)`\.

1. Requeues the job to another compute resource\.

The static and powered up nodes of the disabled compute resource aren't impacted\. Jobs can complete on these nodes\.

This cycle repeats until the job is successfully assigned to a compute resource node or nodes\. For information on node states, see the [Slurm guide for multiple queue mode](multiple-queue-mode-slurm-user-guide-v3.md)\.

If no compute resources are found to run the job, its set to the `PENDING` state until the predefined period of time elapses\. In this case, you can modify the predefined period of time as described in the following section\.

## Insufficient capacity timeout parameter<a name="slurm-short-capacity-fail-mode-parameter-v3"></a>

`insufficient_capacity_timeout`

`insufficient_capacity_timeout` specifies the period of time in seconds that the compute resource is kept in the disabled \(`down`\) state when an insufficient capacity error is detected\.

`insufficient_capacity_timeout` is enabled by default\.

The default `insufficient_capacity_timeout` is 600 seconds\.

If `insufficient_capacity_timeout` is less than or equal to zero, fast insufficient capacity failure\-over mode is disabled\.

You can change the `insufficient_capacity_timeout` value by adding the parameter in the `clustermgtd` config file located at `/etc/parallelcluster/slurm_plugin/parallelcluster_clustermgtd.conf` in the `HeadNode`\.

The parameter can be updated at any time without stopping the compute fleet\.

For example:
+ `insufficient_capacity_timeout=600`:

  If an insufficient capacity error is detected, the compute resource is set to a disabled \(`DOWN`\)\. After 10 minutes it's failed node is set to the `idle~` \(`POWER_SAVING`\) state\.
+ `insufficient_capacity_timeout=60`:

  If an insufficient capacity error is detected, the compute resource is in a disabled \(`DOWN`\)\. After 1 minute it's failed node is set to the `idle~` state\.
+ `insufficient_capacity_timeout=0`:

  Fast insufficient capacity failure\-over mode is disabled\. The compute resource isn't disabled\.

## Fast insufficient capacity fail\-over mode status<a name="slurm-short-capacity-fail-mode-status-v3"></a>

When a cluster is in fast insufficient capacity fail\-over mode, you can check its status and node states\.

### Node states<a name="slurm-short-capacity-fail-mode-nodes-v3"></a>

When a job is submitted to a compute resource dynamic node and a insufficient capacity error is detected, the node is placed in the `down#` state with reason:

```
(Code:InsufficientInstanceCapacity)Failure when resuming nodes.
```

Then powered off nodes \(nodes in `idle~` state\) are set to `down~` with reason:

```
(Code:InsufficientInstanceCapacity)Temporarily disabling node due to insufficient capacity.
```

The job is requeued to other compute resources in the queue\.

The compute resource static nodes and nodes that are `UP` aren't impacted by fast insufficient capacity fail\-over mode\.

Consider the node states shown in the following example\.

```
$ sinfo
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
queue1*   up    infinite    30  idle~ queue1-dy-c-1-[1-15],queue1-dy-c-2-[1-15]
queue2    up    infinite    30  idle~ queue2-dy-c-1-[1-15],queue2-dy-c-2-[1-15]
```

We submit a job to queue1 that requires 1 node:

```
$ sinfo
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
queue1*   up   infinite  1   down# queue1-dy-c-1-1
queue1*   up   infinite  15  idle~ queue1-dy-c-2-[1-15]
queue1*   up   infinite  14  down~ queue1-dy-c-1-[2-15]
queue2    up   infinite  30  idle~ queue2-dy-c-1-[1-15],queue2-dy-c-2-[1-15]
```

Node `queue1-dy-c-1-1` is launched to run the job\. However, the instance failed to launch due to an insufficient capacity error\. Node `queue1-dy-c-1-1` is set to `down`\. The powered off dynamic node within the compute resource \(`queue2-dy-c-1`\) is set to `down`\.

You can check the node reason with `scontrol show nodes`\.

```
$ scontrol show nodes queue1-dy-c-1-1
NodeName=broken-dy-c-2-1 Arch=x86_64 CoresPerSocket=1 
CPUAlloc=0 CPUTot=96 CPULoad=0.00
...
ExtSensorsJoules=n/s ExtSensorsWatts=0 ExtSensorsTemp=n/s
Reason=(Code:InsufficientInstanceCapacity)Failure when resuming nodes [root@2022-03-10T22:17:50]
   
$ scontrol show nodes queue1-dy-c-1-2
NodeName=broken-dy-c-2-1 Arch=x86_64 CoresPerSocket=1 
CPUAlloc=0 CPUTot=96 CPULoad=0.00
...
ExtSensorsJoules=n/s ExtSensorsWatts=0 ExtSensorsTemp=n/s
Reason=(Code:InsufficientInstanceCapacity)Temporarily disabling node due to insufficient capacity [root@2022-03-10T22:17:50]
```

The job is queued to another instance type within the queue compute resources\.

After the `insufficient_capacity_timeout` elapses, nodes in the compute resource are reset to the `idle~` state\.

```
$ sinfo
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
queue1*   up    infinite    30  idle~ queue1-dy-c-1-[1-15],queue1-dy-c-2-[1-15]
queue2    up    infinite    30  idle~ queue2-dy-c-1-[1-15],queue2-dy-c-2-[1-15]
```

After the `insufficient_capacity_timeout` elapses and nodes in the compute resource are reset to the `idle~` state, the Slurm scheduler gives the nodes lower priority\. The scheduler keeps selecting nodes from other queue compute resources with higher weights unless one of the following occurs:
+ A job's submission requirements match the recovered compute resource\.
+ No other compute resources are available because they are at capacity\.
+ `slurmctld` is restarted\.
+ The AWS ParallelCluster compute fleet is stopped and started to power down and power up all nodes\.

### Related logs<a name="slurm-protected-mode-logs-v3"></a>

Logs related to insufficient capacity errors and fast insufficient capacity fail\-over mode can be found in Slurm's `resume` log and `clustermgtd` log in the head node\.

**Slurm `resume` \(`/var/log/parallelcluster/slurm_resume.log`\)**  
Error messages when a node fails to launch due to insufficient capacity:  

```
[slurm_plugin.instance_manager:_launch_ec2_instances] - ERROR - Failed RunInstances request: dcd0c252-90d4-44a7-9c79-ef740f7ecd87
[slurm_plugin.instance_manager:add_instances_for_nodes] - ERROR - Encountered exception when launching instances for nodes (x1) ['queue1-dy-c-1-1']: An error occurred 
(InsufficientInstanceCapacity) when calling the RunInstances operation (reached max retries: 1): We currently do not have sufficient p4d.24xlarge capacity in the 
Availability Zone you requested (us-west-2b). Our system will be working on provisioning additional capacity. You can currently get p4d.24xlarge capacity by not 
specifying an Availability Zone in your request or choosing us-west-2a, us-west-2c.
```

**Slurm `clustermgtd` \(`/var/log/parallelcluster/clustermgtd`\)**  
Compute resource c\-1 in queue1 is disabled due to insufficient capacity:  

```
[slurm_plugin.clustermgtd:_reset_timeout_expired_compute_resources] - INFO - The following compute resources are in down state 
due to insufficient capacity: {'queue1': {'c-1': ComputeResourceFailureEvent(timestamp=datetime.datetime(2022, 4, 14, 23, 0, 4, 769380, tzinfo=datetime.timezone.utc), 
error_code='InsufficientInstanceCapacity')}}, compute resources are reset after insufficient capacity timeout (600 seconds) expired
```
After the insufficient capacity timeout expires, the compute resource is reset, nodes within the compute resources are set to `idle~`:  

```
[root:_reset_insufficient_capacity_timeout_expired_nodes] - INFO - Reset the following compute resources because insufficient capacity 
timeout expired: {'queue1': ['c-1']}
```