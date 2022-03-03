# Best practices<a name="best-practices"></a>

## Best practices: master instance type selection<a name="best-practices-master-instance-type"></a>

Although the master node doesn't execute any job, its functions and its sizing are crucial to the overall performance of the cluster\.

When choosing the instance type to use for your master node you want to evaluate the following items:
+ **Cluster size:** the master node orchestrates the scaling logic of the cluster and is responsible of attaching new nodes to the scheduler\. If you need to scale up and down the cluster of a considerable amount of nodes then you want to give the master node some extra compute capacity\.
+ **Shared file systems:** when using shared file systems to share artifacts between compute nodes and the master node take into account that the master is the node exposing the NFS server\. For this reason you want to choose an instance type with enough network bandwidth and enough dedicated Amazon EBS bandwidth to handle your workflows\.

## Best practices: network performance<a name="best-practices-network-performance"></a>

There are three hints that cover the whole range of possibilities to improve network communication\.
+ **Placement group:** a cluster placement group is a logical grouping of instances within a single Availability Zone\. For more information on placement groups, see [placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\. You can configure the cluster to use your own placement group with `placement_group = your-placement-group-name` or let AWS ParallelCluster create a placement group with the `"compute"` strategy with `placement_group = DYNAMIC`\. For more information, see [`placement_group`](queue-section.md#queue-placement-group) for multiple queue mode and [`placement_group`](cluster-definition.md#placement-group) for single queue mode\.
+ **Enhanced networking:** consider to choose an instance type that supports Enhanced Networking\. For more information, see [enhanced networking on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Instance bandwidth:** the bandwidth scales with instance size, please consider to choose the instance type which better suits your needs, see [Amazon EBS–optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html) and [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html) in the *Amazon EC2 User Guide for Linux Instances*\.