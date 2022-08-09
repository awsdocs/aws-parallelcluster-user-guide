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
+ **Elastic Fabric Adapter:** To support high levels of scaleable inter\-instance communication, consider choosing EFA network interfaces for your network\. The EFA's custom\-built operating system \(OS\) bypass hardware enhances inter\-instance communications with the on\-demand elasticity and flexibility of the AWS cloud\. To configure a single Slurm cluster queue to use EFA, set `enable_efa = true`\. For more information about using EFA with AWS ParallelCluster, see [Elastic Fabric Adapter](efa.md) and [`enable_efa`](queue-section.md#queue-enable-efa)\. For more information about EFA, see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Instance bandwidth:** the bandwidth scales with instance size, please consider to choose the instance type which better suits your needs, see [Amazon EBS–optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html) and [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Best practices: budget alerts<a name="best-practices-budget-alerts"></a>

To manage AWS ParallelCluster resource costs, we recommend that you use AWS Budgets actions to create a budget and defined budget threshold alerts for selected AWS resources\. For more information, see [Configuring a budget action](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-controls.html) in the *AWS Budgets User Guide*\. You can also use Amazon CloudWatch to create a billing alarm\. For more information, see [Creating a billing alarm to monitor your estimated AWS charges](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html)\.

## Best practices: moving a cluster to a new AWS ParallelCluster minor or patch version<a name="best-practices-cluster-upgrades"></a>

Currently each AWS ParallelCluster minor version is self\-contained along with its `pcluster` CLI\. To move a cluster to a new minor or patch version, you must re\-create the cluster using the new version's CLI\.

To optimize the process of moving a cluster to a new minor version or to save your shared storage data for other reasons, we recommend that you use the following best practices\.
+ Save personal data in external volumes, such as Amazon EFS and FSx for Lustre\. By doing this, you can easily move the data from one cluster to another\.
+ Create shared storage systems of the types listed below using the AWS CLI or AWS Management Console:
  + [`[ebs]` section](ebs-section.md)
  + [`[efs]` section](efs-section.md)
  + [`[fsx]` section](fsx-section.md)

  Add them to the new cluster configuration as existing file systems\. This way, they are preserved when you delete the cluster and can be attached to a new cluster\. Shared storage systems generally incur charges whether they are attached or detached from a cluster\.

  We recommend that you use Amazon EFS, or Amazon FSx for Lustre file systems because they can be attached to multiple clusters at the same time and you can attach them to the new cluster before deleting the old cluster\. For more information, see [Mounting Amazon EFS file systems](https://docs.aws.amazon.com/efs/latest/ug/mounting-fs.html) in the *Amazon EFS User Guide* and [Accessing FSx for Lustre file systems](https://docs.aws.amazon.com/fsx/latest/LustreGuide/accessing-fs.html) in the *Amazon FSx for Lustre Lustre User Guide*\.
+ Use [custom bootstrap actions](pre_post_install.md) to customize your instances rather than a custom AMI\. This optimizes the creation process because a new custom AMI doesn't need to be created for each new version\.
+ Recommended sequence\.

  1. Update the cluster configuration to use existing file systems definitions\.

  1. Verify the `pcluster` version and update it if needed\.

  1. Create and test the new cluster\.
     + Make sure your data is available in the new cluster\.
     + Make sure your application works in the new cluster\.

  1. If you're new cluster is fully tested and operational and you're sure you aren't going to use the old cluster, delete it\.