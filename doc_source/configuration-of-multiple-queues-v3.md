# Configuration of Multiple Queues<a name="configuration-of-multiple-queues-v3"></a>

**Configuration of Multiple Queues**

AWS ParallelCluster version 3\.0\.0 supports multiple queues via specification in the configuration file when the `Scheduler` is set to `slurm` and more than one ``SlurmQueues`` has been specified\. This mode allows different instance types to coexist in the compute nodes as specified in the ``ComputeResources`` portion of the configuration\. The compute resources that contain the different instance types can scale up or down as needed\. Note that currently there is a maximum of up to five \(5\) `ComputeResources` per queue and a maximum of up to ten \(10\) queues that can be specified\. Each of the specified queues corresponds to a partition in the Slurm Workload Manager\. For more information, see [Slurm Workload Manager \(`slurm`\)](slurm-workload-manager-v3.md)\.

**Node Counts**

Each `ComputeResources` in a queue must have a unique `Name` and `InstanceType` as well as an optionally specified `MinCount` and `MaxCount` specifying the range of instances for that `ComputeResources` within the queue\. Each `ComputeResources` will be composed of static nodes numbered from 1 to the value of `MinCount`\. Dynamic nodes for each `ComputeResources` are numbered from the `MinCount` to the `MaxCount`\.

**Example Configuration**

Provided below is an example section from a cluster configuration file\. In this configuration there are two queues named `queue1` and `queue2` respectively and each of the queues has `ComputeResources` defined with a specified `MaxCount`\.

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

The instances that are launched into the compute fleet are dynamically assigned\. To help manage this, hostnames are generated for each node\. The format of the hostname is as follows:

 `$HOSTNAME=$QUEUE-$STATDYN-$COMPUTE_RESOURCE-$NODENUM` 
+ `$QUEUE` is the name of the queue\. For example, if the `SlurmQueues` has an entry with the `Name` set to “`queue-name`” then “`$QUEUE`” is “`queue-name`”\.
+  `$STATDYN` is `st` for static nodes or `dy` for dynamic nodes\. 
+  `$COMPUTE_RESOURCE` will be the `Name` of the `ComputeResource` corresponding to this node\.
+  `$NODENUM` is the number of the node\. `$NODENUM` is between one \(1\) and the value of `MinCount` for static nodes and between one \(1\) and `MaxCount-MinCount` for dynamic nodes\.

From the example configuration file above, a given node from `queue1` and compute resource `c5xlarge` would have a hostname: `queue1-dy-c5xlarge-1`\.

Both hostnames and fully\-qualified domain names \(FQDN\) are created using Amazon Route 53 hosted zones\. The FQDN is `$HOSTNAME.$CLUSTERNAME.pcluster`, where `$CLUSTERNAME` is the name of the cluster\.