# Best practices<a name="best-practices-v3"></a>

## Best practices: head node instance type selection<a name="best-practices-head-node-instance-type"></a>

Although the head node doesn't execute any job, its functions and its sizing are crucial to the overall performance of the cluster\. When choosing the instance type to use for your head node you want to evaluate the following items:

**Cluster size:** the head node orchestrates the scaling logic of the cluster and is responsible of attaching new nodes to the scheduler\. If you need to scale up and down the cluster of a considerable amount of nodes then you want to give the head node some extra compute capacity\.

**Shared file systems:** when using shared file systems to share artifacts between compute nodes and the head node take into account that the head node is the node exposing the NFS server\. For this reason you want to choose an instance type with enough network bandwidth and enough dedicated Amazon EBS bandwidth to handle your workflows\.

## Best practices: network performance<a name="best-practices-network-performance-v3"></a>

Network performance is critical to ensuring high performance computing \(HPC\) applications perform as expected\. We recommend these three best practices to optimize your network performance\.
+ **Placement group:** a cluster placement group is a logical grouping of instances within a single Availability Zone\. For more information on placement groups, see [placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\. If you are using Slurm, you can configure each Slurm queue to use a cluster placement group by specifying a `PlacementGroup` in the queue's [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) settings\.

  ```
  Networking:
    PlacementGroup:
      Enabled: true
      Id: your-placement-group-name
  ```

  Or let AWS ParallelCluster create a placement group with:

  ```
  Networking:
    PlacementGroup:
      Enabled: true
  ```

  For more information, see [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking)\.
+ **Enhanced networking:** consider choosing an instance type that supports enhanced networking\. This applies to all [current generation instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#current-gen-instances)\. For more information, see [enhanced networking on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Instance bandwidth:** the bandwidth scales with instance size, please consider to choose the instance type which better suits your needs, see [Amazon EBS–optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html) and [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html) in the *Amazon EC2 User Guide for Linux Instances*\.