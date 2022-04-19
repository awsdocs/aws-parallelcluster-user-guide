# Configuration of Multiple Queues<a name="configuration-of-multiple-queues-v3"></a>

**Configuration of Multiple Queues**

With AWS ParallelCluster version 3, you can configure multiple queues by setting the `Scheduler` to `slurm` and specifying more than one queue for ``SlurmQueues`` in the configuration file\. In this mode, different instance types coexist in the compute nodes that are specified in the ``ComputeResources`` section of the configuration file\. `ComputeResources` with different instance types are scaled up or down as needed for the ``SlurmQueues``\. Currently you can specify a maximum of up to five \(5\) `ComputeResources` per queue and a maximum of up to ten \(10\) ``SlurmQueues``\. Each specified queue corresponds to a partition in the Slurm Workload Manager\. For more information, see [Slurm Workload Manager \(`slurm`\)](slurm-workload-manager-v3.md)\.

**Node Counts**

Each compute resource in `ComputeResources` for a queue must have a unique `Name`, `InstanceType`, `MinCount`, and `MaxCount`\. `MinCount` and `MaxCount` have default values that define the range of instances for a compute resource in `ComputeResources` for a queue\. You can also specify your own values for `MinCount` and `MaxCount`\. Each compute resource in `ComputeResources` is composed of static nodes numbered from 1 to the value of `MinCount` and dynamic nodes numbered from the value of `MinCount` to the value of `MaxCount`\.

**Example Configuration**

The following is an example of a [Scheduling](Scheduling-v3.md) section for a cluster configuration file\. In this configuration there are two queues named `queue1` and `queue2` and each of the queues has `ComputeResources` with a specified `MaxCount`\.

```
Scheduling:
  Scheduler: slurm
  SlurmQueues:
  - Name: queue1
    ComputeResources:
    - InstanceType: c5.xlarge
      MaxCount: 5
      Name: c5xlarge
    - InstanceType: c4.xlarge
      MaxCount: 5
      Name: c4xlarge
  - Name: queue2
    ComputeResources:
    - InstanceType: c5.xlarge
      MaxCount: 5
      Name: c5xlarge
```

**Hostnames**

The instances that are launched into the compute fleet are dynamically assigned\. Hostnames are generated for each node\. The format of the hostname is as follows:

 `$HOSTNAME=$QUEUE-$STATDYN-$COMPUTE_RESOURCE-$NODENUM` 
+ `$QUEUE` is the name of the queue\. For example, if the `SlurmQueues` section has an entry with the `Name` set to “`queue-name`” then “`$QUEUE`” is “`queue-name`”\.
+  `$STATDYN` is `st` for static nodes or `dy` for dynamic nodes\. 
+  `$COMPUTE_RESOURCE` is the `Name` of the `ComputeResource` corresponding to this node\.
+  `$NODENUM` is the number of the node\. `$NODENUM` is between one \(1\) and the value of `MinCount` for static nodes and between one \(1\) and `MaxCount-MinCount` for dynamic nodes\.

From the example configuration file above, a given node from `queue1` and compute resource `c5xlarge` has a hostname: `queue1-dy-c5xlarge-1`\.

Both hostnames and fully\-qualified domain names \(FQDN\) are created using Amazon Route 53 hosted zones\. The FQDN is `$HOSTNAME.$CLUSTERNAME.pcluster`, where `$CLUSTERNAME` is the name of the cluster\.