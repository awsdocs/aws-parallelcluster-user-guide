# Slurm cluster protected mode<a name="slurm-protected-mode-v3"></a>

When a cluster runs with protected mode enabled, AWS ParallelCluster monitors and tracks compute node bootstrap failures as they're launched to detect whether or not the failures are occurring continuously\.

If either of the following is detected, the cluster enters protected mode:

1. Consecutive bootstrap failures occur continuously with no successful compute node launches\.

1. The failure count for a queue reaches a pre\-defined threshold\.

After the cluster enters protected mode, AWS ParallelCluster disables the queue or queues with failures at or above the pre\-defined threshold\.

Slurm cluster protected mode was added in AWS ParallelCluster version 3\.0\.0\.

You can use protected mode to reduce the time and resources spent on compute node bootstrap failure cycling\.

## Protected node parameter<a name="slurm-protected-mode-parameter-v3"></a>

`protected_failure_count`

`protected_failure_count` specifies the number of consecutive failures that activate cluster protected node\.

The default `protected_failure_count` is 10 and protected mode is enabled\.

If `protected_failure_count` is less than or equal to zero, protected mode is disabled\.

You can change the `protected_failure_count` value by adding the parameter in the `clustermgtd` config file that's located at `/etc/parallelcluster/slurm_plugin/parallelcluster_clustermgtd.conf` in the `HeadNode`\.

You can update this parameter at any time and don't need to stop the compute fleet to do so\. If a launch succeeds in a queue before the failure count reaches `protected_failure_count`, the failure count is reset to zero\.

## Check cluster status in protected mode<a name="slurm-protected-mode-status-v3"></a>

When a cluster is in protected mode, you can check the compute fleet status and node states\.

### Compute fleet status<a name="slurm-protected-mode-compute-fleet-v3"></a>

The status of the compute fleet is `PROTECTED` in a cluster running in protected mode\.

```
$ pcluster describe-compute-fleet --cluster-name <cluster-name> --region <region-id>
{
   "status": "PROTECTED",
   "lastStatusUpdatedTime": "2022-04-22T00:31:24.000Z"
}
```

### Node status<a name="slurm-protected-mode-nodes-v3"></a>

To learn which queues \(partitions\) have bootstrap failures that have activated protected mode, log in to the cluster and run the `sinfo` command\. Partitions with bootstrap failures at or above `protected_failure_count` are in the `INACTIVE state`\. Partitions without bootstrap failures at or above `protected_failure_count` are in the `UP` state and work as expected\.

`PROTECTED` status doesn't impact running jobs\. If jobs are running on a partition with bootstrap failures at or above `protected_failure_count`, the partition is set to `INACTIVE` after the running jobs complete\.

Consider the node states shown in the following example\.

```
$ sinfo
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
queue1* inact infinite 10 down% queue1-dy-c5xlarge-[1-10]
queue1* inact infinite 3490 idle~ queue1-dy-c5xlarge-[11-3500]
queue2 up infinite 10 idle~ queue2-dy-c5xlarge-[1-10]
```

Partition `queue1` is `INACTIVE` because 10 consecutive continuous bootstrap failures were detected\.

Instances behind nodes `queue1-dy-c5xlarge-[1-10]` launched but failed to join the cluster because of an unhealthy status\.

The cluster is in protected mode\.

Partition `queue2` isn't impacted by the bootstrap failures in `queue1`\. It's in the `UP` state and can still run jobs\.

## How to deactivate protected mode<a name="slurm-protected-mode-exit-v3"></a>

After the bootstrap error has been resolved, you can run the following command to take the cluster out of protected mode\.

```
$ pcluster update-compute-fleet --cluster-name <cluster-name> \
  --region <region-id> \
  --status START_REQUESTED
```

## Bootstrap failures that activate protected mode<a name="slurm-protected-mode-failures-v3"></a>

Bootstrap errors that activate protected mode are subdivided into the following three types\. To identify the type and issue, you can check if AWS ParallelCluster generated logs\. If the logs were generated, you can check them for error details\. For more information, see [Retrieving and preserving cluster logs](troubleshooting-v3-get-logs.md)\.

1. **Bootstrap error that causes an instance to self\-terminate**\.

   An instance fails early in the bootstrap process, such as an instance that self\-terminates because of errors in the [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) \\ [`CustomActions`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-CustomActions) \\ [`OnNodeStart`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeStart) \| [`OnNodeConfigured`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeConfigured) script\.

   For dynamic nodes, look for errors similar to the following\.

   ```
   Node bootstrap error: Node ... is in power up state without valid backing instance
   ```

   For static nodes, look in the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) for errors similar to the following\.

   ```
   Node bootstrap error: Node ... is in power up state without valid backing instance
   ```

1. **Nodes `resume_timeout` or `node_replacement_timeout` expires**\.

   An instance can't join cluster within the `resume_timeout` \(for dynamic nodes\) or `node_replacement_timeout` \(for static nodes\)\. It doesn't self\-terminate before the timeout\. For example, networking isn't set up correctly for the cluster and the node is set to the `DOWN` state by Slurm after the timeout expires\.

   For dynamic nodes, look for errors similar to the following\.

   ```
   Node bootstrap error: Resume timeout expires for node
   ```

   For static nodes, look in the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) for errors similar to the following\.

   ```
   Node bootstrap error: Replacement timeout expires for node ... in replacement.
   ```

1. **Nodes fail health check**\.

   An instance behind the node fails an EC2 health check or scheduled event health check, and the nodes are treated as bootstrap failure nodes\. In this case, the instance is terminated for a reason outside of AWS ParallelCluster control\.

   Look in the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) for errors similar to the following\.

   ```
   Node bootstrap error: Node %s failed during bootstrap when performing health check.
   ```

## How to debug protected mode<a name="slurm-protected-mode-debug-v3"></a>

After you learned that your cluster is in protected status, and if AWS ParallelCluster generated `clustermgtd` logs from the `HeadNode` and the `cloud-init-output` logs from problematic compute nodes, you can check the logs for error details\. For more information about how to retrieve logs, see [Retrieving and preserving cluster logs](troubleshooting-v3-get-logs.md)\.

**`clustermgtd` log\(`/var/log/parallelcluster/clustermgtd`\) on the head node**

Log messages show which partitions have bootstrap failures and the corresponding bootstrap failure count\.

```
[slurm_plugin.clustermgtd:_handle_protected_mode_process] - INFO - Partitions  
bootstrap failure count: {'queue1': 2}, cluster will be set into protected mode if protected failure count reach threshold.
```

In the `clustermgtd` log, search for `Found the following bootstrap failure nodes` to find which node failed to bootstrap\.

```
[slurm_plugin.clustermgtd:_handle_protected_mode_process] - WARNING - 
Found the following bootstrap failure nodes: (x2)  ['queue1-st-c5large-1(192.168.110.155)',  'broken-st-c5large-2(192.168.65.215)']
```

In the `clustermgtd` log, search for `Node bootstrap error` to find the failure reason\.

```
[slurm_plugin.clustermgtd:_is_node_bootstrap_failure] - WARNING - Node bootstrap error: 
Node broken-st-c5large-2(192.168.65.215) is currently in  replacement and no backing instance
```

**`cloud-init-output` log\(`/var/log/cloud-init-output.log`\) on the compute nodes**

After obtaining the bootstrap failure node private IP address in the `clustermgtd` log, you can find the corresponding compute node log by either logging into the compute node or by following the guidance in [Retrieving and preserving cluster logs](troubleshooting-v3-get-logs.md) to retrieve logs\. In most cases, the `/var/log/cloud-init-output` log from the problematic node shows the step that causes the compute node bootstrap failure\.