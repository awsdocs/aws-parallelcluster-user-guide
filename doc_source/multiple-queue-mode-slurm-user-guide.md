# Slurm guide for multiple queue mode<a name="multiple-queue-mode-slurm-user-guide"></a>

AWS ParallelCluster version 2\.9\.0 introduced multiple queue mode and a new scaling architecture for Slurm Workload Manager \(Slurm\)\.

The following sections provide a general overview on using a Slurm cluster with the newly introduced scaling architecture\.

## Overview<a name="multiple-queue-mode-slurm-user-guide-overview"></a>

The new scaling architecture is based on Slurm’s [Cloud Scheduling Guide](https://slurm.schedmd.com/elastic_computing.html) and power saving plugin\. For more information about the power saving plugin, see [Slurm Power Saving Guide](https://slurm.schedmd.com/power_save.html)\. In the new architecture, resources that can potentially be made available for a cluster are typically predefined in the Slurm configuration as cloud nodes\.

## Cloud node lifecycle<a name="multiple-queue-mode-slurm-user-guide-cloud-node-lifecycle"></a>

Throughout their lifecycle, cloud nodes enter several if not all of the following states: `POWER_SAVING`, `POWER_UP` \(`pow_up`\), `ALLOCATED` \(`alloc`\), and `POWER_DOWN` \(`pow_dn`\)\. In some cases, a cloud node might enter the `OFFLINE` state\. The following list details several aspects of these states in the cloud node lifecycle\.
+ A node in a `POWER_SAVING` state appears with a `~` suffix \(for example `idle~`\) in `sinfo`\. In this state, there is no EC2 instance backing the node\. However, Slurm can still allocate jobs to the node\.
+ A node transitioning to a `POWER_UP` state appears with a `#` suffix \(for example`idle#`\) in `sinfo`\.
+ When Slurm allocates job to a node in a `POWER_SAVING` state, the node automatically transfers to a `POWER_UP` state\. Otherwise, nodes can be placed in the `POWER_UP` state manually using the `scontrol update nodename=nodename state=power_up` command\. In this stage, the `ResumeProgram` is invoked, and EC2 instances are launched and configured to back a `POWER_UP` node\.
+ A node that is currently available for use appears without any suffix \(for example `idle`\) in `sinfo`\. After the node is setup and joins the cluster, it becomes available to run jobs\. In this stage, the node is properly configured and ready for use\. As a general rule, we recommend that the number of instances in EC2 be the same as the number of available nodes\. In most cases, static nodes are always available after the cluster is created\.
+ A node that is transitioning to a `POWER_DOWN` state appears with a `%` suffix \(for example `idle%`\) in `sinfo`\. Dynamic nodes automatically enter `POWER_DOWN` state after [`scaledown_idletime`](scaling-section.md#scaledown-idletime)\. In contrast, static nodes in most cases aren't powered down\. However, nodes can be placed in the `POWER_DOWN` state manually using the `scontrol update nodename=nodename state=powering_down` command\. In this state, the instance associated with a node is terminated, and node is reset back to the `POWER_SAVING` state to future use after [`scaledown_idletime`](scaling-section.md#scaledown-idletime)\. The `scaledown-idletime` setting is saved to the Slurm configuration as the `SuspendTimeout` setting\.
+ A node that is offline appears with a `*` suffix \(for example `down*`\) in `sinfo`\. A node goes offline if Slurm controller can't contact the node or if the static nodes are disabled and the backing instances are terminated\.

Now consider the node states shown in the following `sinfo` example\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa          up   infinite      4  idle~ efa-dy-c5n18xlarge-[1-4]
efa          up   infinite      1   idle efa-st-c5n18xlarge-1
gpu          up   infinite      1  idle% gpu-dy-g38xlarge-1
gpu          up   infinite      9  idle~ gpu-dy-g38xlarge-[2-10]
ondemand     up   infinite      2   mix# ondemand-dy-c52xlarge-[1-2]
ondemand     up   infinite     18  idle~ ondemand-dy-c52xlarge-[3-10],ondemand-dy-t2xlarge-[1-10]
spot*        up   infinite     13  idle~ spot-dy-c5xlarge-[1-10],spot-dy-t2large-[1-3]
spot*        up   infinite      2   idle spot-st-t2large-[1-2]
```

The `spot-st-t2large-[1-2]` and `efa-st-c5n18xlarge-1` nodes already have backing instances set up and are available for use\. The `ondemand-dy-c52xlarge-[1-2]` nodes are in the `POWER_UP` state, and they should be available within a few minutes\. The `gpu-dy-g38xlarge-1` node is in the `POWER_DOWN` state, and it will transition into `POWER_SAVING` state after [`scaledown_idletime`](scaling-section.md#scaledown-idletime) \(defaults to 120 seconds\)\.

All of the other nodes are in `POWER_SAVING` state with no EC2 instances backing them\.

## Working with an available node<a name="multiple-queue-mode-slurm-user-guide-working-with-available-nodes"></a>

An available node is backed by an EC2 instance\. By default, the node name can be used to directly ssh into the instance \(for example `ssh efa-st-c5n18xlarge-1`\)\. The private IP address of the instance can be retrieved using the `scontrol show nodes nodename` command and checking the `NodeAddr` field\. For nodes that aren't available, the `NodeAddr` field shouldn't point to a running EC2 instance, but rather should be the same as the node name\.

## Job states and submission<a name="multiple-queue-mode-slurm-user-guide-job-states"></a>

Jobs submitted in most cases are immediately allocated to nodes in the system, or placed in pending if all the nodes are allocated\.

If nodes allocated for a job include any nodes in a `POWER_SAVING` state, the job starts out with a `CF`, or `CONFIGURING` state\. At this time, the job waits for the nodes in the `POWER_SAVING` state to transition to `POWER_UP` state and become available\.

After all nodes allocated for a job are available, the job enters the `RUNNING` \(`R`\) state\.

By default, all jobs are submitted to the default queue \(known as a partition in Slurm\)\. This is signified by a `*` suffix after the queue name\. You can select a queue using the `-p` job submission option\.

All nodes are configured with the following features, which can be used in job submission commands:
+ An instance type \(for example `c5.xlarge`\)
+ A node type \(This is either `dynamic` or `static`\.\)

You can see all of the features available for a particular node by using the `scontrol show nodes nodename` command and checking the `AvailableFeatures` list\.

Another consideration are jobs\. First consider the initial state of the cluster, which you can view by running the `sinfo` command\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa          up   infinite      4  idle~ efa-dy-c5n18xlarge-[1-4]
efa          up   infinite      1   idle efa-st-c5n18xlarge-1
gpu          up   infinite     10  idle~ gpu-dy-g38xlarge-[1-10]
ondemand     up   infinite     20  idle~ ondemand-dy-c52xlarge-[1-10],ondemand-dy-t2xlarge-[1-10]
spot*        up   infinite     13  idle~ spot-dy-c5xlarge-[1-10],spot-dy-t2large-[1-3]
spot*        up   infinite      2   idle spot-st-t2large-[1-2]
```

Note that `spot` is the default queue\. It is indicated by the `*` suffix\.

Submit a job to one static node to the default queue \(`spot`\)\.

```
$ sbatch --wrap "sleep 300" -N 1 -C static
```

Submit a job to one dynamic node to the `EFA` queue\.

```
$ sbatch --wrap "sleep 300" -p efa -C dynamic
```

Submit a job to eight \(8\) `c5.2xlarge` nodes and two \(2\) `t2.xlarge` nodes to the `ondemand` queue\.

```
$ sbatch --wrap "sleep 300" -p ondemand -N 10 -C "[c5.2xlarge*8&t2.xlarge*2]"
```

Submit a job to one GPU node to the `gpu` queue\.

```
$ sbatch --wrap "sleep 300" -p gpu -G 1
```

Now considerthe state of the jobs using the `squeue` command\.

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                12  ondemand     wrap   ubuntu CF       0:36     10 ondemand-dy-c52xlarge-[1-8],ondemand-dy-t2xlarge-[1-2]
                13       gpu     wrap   ubuntu CF       0:05      1 gpu-dy-g38xlarge-1
                 7      spot     wrap   ubuntu  R       2:48      1 spot-st-t2large-1
                 8       efa     wrap   ubuntu  R       0:39      1 efa-dy-c5n18xlarge-1
```

Jobs 7 and 8 \(in the `spot` and `efa` queues\) are already running \(`R`\)\. Jobs 12 and 13 are still configuring \(`CF`\), probably waiting for the instances to become available\.

```
# Nodes states corresponds to state of running jobs
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa          up   infinite      3  idle~ efa-dy-c5n18xlarge-[2-4]
efa          up   infinite      1    mix efa-dy-c5n18xlarge-1
efa          up   infinite      1   idle efa-st-c5n18xlarge-1
gpu          up   infinite      1   mix~ gpu-dy-g38xlarge-1
gpu          up   infinite      9  idle~ gpu-dy-g38xlarge-[2-10]
ondemand     up   infinite     10   mix# ondemand-dy-c52xlarge-[1-8],ondemand-dy-t2xlarge-[1-2]
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[9-10],ondemand-dy-t2xlarge-[3-10]
spot*        up   infinite     13  idle~ spot-dy-c5xlarge-[1-10],spot-dy-t2large-[1-3]
spot*        up   infinite      1    mix spot-st-t2large-1
spot*        up   infinite      1   idle spot-st-t2large-2
```

## Node state and features<a name="multiple-queue-mode-slurm-user-guide-node-state-features"></a>

In most cases, node states are fully managed by AWS ParallelCluster according to the specific processes in the cloud node lifecycle described earlier in this topic\.

However, AWS ParallelCluster also replaces or terminates unhealthy nodes in `DOWN` and `DRAINED` states and nodes that have unhealthy backing instances\. For more information, see [`clustermgtd`](processes.md#clustermgtd)\.

## Partition states<a name="multiple-queue-mode-slurm-user-guide-partition-states"></a>

AWS ParallelCluster supports the following partition states\. A Slurm partition is a queue in AWS ParallelCluster\.
+ `UP`: Indicates the partition is in an active state\. This is the default state of a partition\. In this state, all nodes in the partition are active and available for use\.
+ `INACTIVE`: Indicates the partition is in the inactive state In this state, all instances backing nodes of an inactive partition are terminated\. New instances aren't launched for nodes in an inactive partition\.

## pcluster start and stop<a name="multiple-queue-mode-slurm-user-guide-pcluster-start-stop"></a>

When [`pcluster stop`](pcluster.stop.md) is run, all partitions are placed in the `INACTIVE` state, and the AWS ParallelCluster processes keep the partitions in the `INACTIVE` state\.

When [`pcluster start`](pcluster.start.md) is run, all partitions are initially placed in the `UP` state\. However,, AWS ParallelCluster processes don't keep the partition in an `UP` state\. You need to change partition states manually\. All static nodes become available after a few minutes\. Note that setting a partition to `UP` doesn't power up any dynamic capacity\. If [`initial_count`](compute-resource-section.md#compute-resource-initial-count) is greater than [`max_count`](compute-resource-section.md#compute-resource-max-count), then [`initial_count`](compute-resource-section.md#compute-resource-initial-count) might not be satisfied when the partition state is changed to the `UP` state\.

When [`pcluster start`](pcluster.start.md) and [`pcluster stop`](pcluster.stop.md) are running, you can check the state of the cluster by running the [`pcluster status`](pcluster.status.md) command and checking the `ComputeFleetStatus`\. The following lists possible states:
+ `STOP_REQUESTED`: The [`pcluster stop`](pcluster.stop.md) request is sent to the cluster\.
+ `STOPPING`: The `pcluster` process is currently stopping the cluster\.
+ `STOPPED`: The `pcluster` process finished the stopping process, all partitions are in `INACTIVE` state, and all compute instances are terminated\.
+ `START_REQUESTED`: The [`pcluster start`](pcluster.start.md) request is sent to the cluster\.
+ `STARTING`: The `pcluster` process is currently starting the cluster
+ `RUNNING`: The `pcluster` process finished the starting process, all partitions are in `UP` state, and static nodes will be available after a few minutes\.

## Manual control on queues<a name="multiple-queue-mode-slurm-user-guide-manual-control-queue"></a>

In some cases, you may want to have some manual control over the nodes or queue \(known as a partition in Slurm\) in a cluster\. You can manage nodes in a cluster through the following common procedures\.
+ Power up dynamic nodes in `POWER_SAVING` state: Run the `scontrol update nodename=nodename state=power_up` command orsubmit a placeholder `sleep 1` job requesting for a certain number of nodes and rely on Slurm to power up required number of nodes\.
+ Power down dynamic nodes prior to [`scaledown_idletime`](scaling-section.md#scaledown-idletime): Set dynamic nodes to `DOWN` with the `scontrol update nodename=nodename state=down` command\. AWS ParallelCluster automatically terminates and resets the downed dynamic nodes\. In general, we don't recommend setting nodes to `POWER_DOWN` directly by using the `scontrol update nodename=nodename state=power_down` command\. This is because AWS ParallelCluster automatically handles the power down process\. No manual is necessary\.Therefore, we recommend that you try to set nodes to `DOWN` whenever possible instead\.
+ Disable a queue \(partition\) or stop all static nodes in specific partition: Set specific the queue to `INACTIVE` with the `scontrol update partition=queue name state=inactive` command\. Doing this terminates all instances backing nodes in the partition\.\.
+ Enable a queue \(partition\): Set specific the queue to `INACTIVE` with the `scontrol update partition=queue name state=up` command\.

## Scaling behavior and adjustments<a name="multiple-queue-mode-slurm-user-guide-scaling-behavior"></a>

Here is an example of the normal scaling workflow:
+ The scheduler receives a job that requires two nodes\.
+ The scheduler transitions two nodes to a `POWER_UP` state, and calls `ResumeProgram` with the node names \(for example `queue1-dy-c5xlarge-[1-2]`\)\.
+ `ResumeProgram` launches two EC2 instances and assigns the private IP addresses and hostnames of `queue1-dy-c5xlarge-[1-2]`, waiting for `ResumeTimeout` \(the default period is 60 minutes \(1 hour\)\) before resetting the nodes\.
+ Instances are configured and join the cluster\. Job starts running on instances\.
+ Job is done\.
+ After the configured `SuspendTime` has elapsed \(which is set to [`scaledown_idletime`](scaling-section.md#scaledown-idletime)\), the instances are put in the `POWER_SAVING` state by the scheduler\. Scheduler places `queue1-dy-c5xlarge-[1-2]` into `POWER_DOWN` state and calls `SuspendProgram` with the node names\.
+ `SuspendProgram` is called for two nodes\. Nodes remain in the `POWER_DOWN` state, for example, by remaining `idle%` for a `SuspendTimeout` \(the default period is 120 seconds \(2 minutes\)\)\. After `clustermgtd` detects that nodes are powering down, it terminates the backing instances\. Then, it configures `queue1-dy-c5xlarge-[1-2]` into idle state and resets private IP address and hostname so they can be power up for future jobs again\.

Now, if things go wrong and an instance for a particular node can't be launched for some reason, then the following happens\.
+ Scheduler receives a job that requires two nodes\.
+ Scheduler places two cloud bursting nodes into `POWER_UP` state and calls `ResumeProgram` with the nodenames, \(for example `queue1-dy-c5xlarge-[1-2]`\)\.
+ `ResumeProgram` launches only one \(1\) EC2 instance and configures `queue1-dy-c5xlarge-1`, but it failed to launch an instance for `queue1-dy-c5xlarge-2`\.
+ `queue1-dy-c5xlarge-1` will not be affected and will come online after reaching `POWER_UP` state\.
+ `queue1-dy-c5xlarge-2` is placed in `POWER_DOWN` state, and the job is requeued automatically because Slurm detects a node failure\.
+ `queue1-dy-c5xlarge-2` becomes available after `SuspendTimeout` \(the default is 120 seconds \(2 minutes\)\)\. In the meantime, the job is requeued and can start running on another node\.
+ The above process is repeated until the job can run on an available node without a failure occurring\.

There are two timing parameters that can be adjusted if needed\.
+ `ResumeTimeout` \(the default is 60 minutes \(1 hour\)\): `ResumeTimeout` controls the time Slurm waits before putting the node the down state\.
  + It might be useful to extend this if your pre/post installation process takes nearly that long\.
  + This is also the maximum time that AWS ParallelCluster waits before replacing or resetting a node if there is an issue\. Compute nodes self\-terminate if any error occurs during launch or setup\. Next, AWS ParallelCluster processes also replaces the node when it sees that the instance is terminated\.
+ `SuspendTimeout` \(the default is 120 seconds \(2 minutes\)\): `SuspendTimeout` controls how quickly nodes get placed back into the system and ready for use again\.
  + A shorter `SuspendTimeout` would mean that nodes will be reset more quickly, and Slurm is able to try to launch instances more frequently\.
  + A longer `SuspendTimeout` makes failed nodes reset more slowly\. In the meantime, Slurm tires to use other nodes\. If `SuspendTimeout` is more than a few minutes, Slurm tries to cycle through all nodes in the system\. A longer `SuspendTimeout` might be beneficial for large\-scale systems \(over1,000 nodes\) for reducing stress on Slurm by frequently re\-queuing failing jobs\.
  + Note that `SuspendTimeout` doesn't refer to the time AWS ParallelCluster waited to terminate a backing instance for a node\. Backing instances for `power down` nodes are immediately terminated\. The terminate process usually is finished a few minutes\. However, during this time, the node remains in the power down state and not available for use in the scheduler\.

## Logs for new architecture<a name="multiple-queue-mode-slurm-user-guide-logs"></a>

The following lit contains the key logs for the multiple queue architecture\. The log stream name used with Amazon CloudWatch Logs has the format `{hostname}.{instance_id}.{logIdentifier}`, where *logIdentifier* follows the log names\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs.md)\.
+ `ResumeProgram`:

  `/var/log/parallelcluster/slurm_resume.log` \(`slurm_resume`\)
+ `SuspendProgram`:

  `/var/log/parallelcluster/slurm_suspend.log` \(`slurm_suspend`\)
+ `clustermgtd`:

  `/var/log/parallelcluster/clustermgtd.log` \(`clustermgtd`\)
+ `computemgtd`:

  `/var/log/parallelcluster/computemgtd.log` \(`computemgtd`\)
+ `slurmctld`:

  `/var/log/slurmctld.log` \(`slurmctld`\)
+ `slurmd`:

  `/var/log/slurmd.log` \(`slurmd`\)

## \[Optional\] Common issues and how to debug:<a name="multiple-queue-mode-slurm-user-guide-common-issues"></a>

**Nodes that failed to launch, power up, or join the cluster: **
+ Dynamic nodes:
  + Check the `ResumeProgram` log to see if `ResumeProgram` was ever called with the node\. If not, check the `slurmctld` log to determine if Slurm ever tried to call `ResumeProgram` with the node\. Note that incorrect permissions on `ResumeProgram` might cause it to fail silently\.
  + If `ResumeProgram` is called, check to see if an instance was launched for the node\. If the instance can't be launched, there should be clear error message as to why the instance failed to launch\.
  + If an instance was launched, there may have been some problem during the bootstrap process\. Find the corresponding private IP address and instance ID from the `ResumeProgram` log and look at corresponding bootstrap logs for the specific instance in CloudWatch Logs\.
+ Static nodes:
  + Check the `clustermgtd` log to see if instances were launched for the node\. If not, there should be clear errors on why the instances failed to launch\.
  + If an instance was launched, there is some problem during bootstrap process\. Find the corresponding private IP and instance ID from the `clustermgtd` log and look at corresponding bootstrap logs for the specific instance in CloudWatch Logs\.

**Nodes replaced or terminated unexpectedly, node failures**
+ Nodes replaced/terminated unexpectedly
  + In most cases, `clustermgtd` handles all node maintenance actions\. To check if `clustermgtd` replaced or terminated a node, check the `clustermgtd` log\.
  + If `clustermgtd` replaced or terminated the node, there should be a message indicating the reason for the action\. If the reason is scheduler related \(for example, the node was `DOWN`\), check in the `slurmctld` log for more details\. If the reason is EC2 related, use tools to check status or logs for that instance\. For example, you can check if the instance had scheduled events or failed EC2 health status checks\.
  + If `clustermgtd` didn't terminate the node, check if `computemgtd` terminated the node or if EC2 terminated the instance to reclaim a spot instance\.
+ Node failures
  + In most cases, jobs are automatically requeued if a node failed\. Look in the `slurmctld` log to see why a job or a node failed and analyze the situation from there\.

**Failure when replacing or terminating instances, failure when powering down nodes**
+ In general, `clustermgtd` handles all expected instance termination actions\. Look in the `clustermgtd` log to see why it failed to replace or terminate a node\.
+ For dynamic nodes failing [`scaledown_idletime`](scaling-section.md#scaledown-idletime), look in the `SuspendProgram` log to see if a program by `slurmctld` with the specific node as argument\. Note `SuspendProgram` doesn't actually perform any specific action\. Rather, it only logs when it’s called\. All instance termination and `NodeAddr` reset are completed by `clustermgtd`\. Slurm puts nodes into `IDLE` after `SuspendTimeout`\.

**Other issues**
+ AWS ParallelCluster doesn't make job allocation or scaling decisions\. It simple tries to launch, terminate, and maintain resources according to Slurm’s instructions\.

  For issues regarding job allocations, node allocation and scaling decision, look at the `slurmctld` log for errors\. 