# Best practices<a name="best-practices-v3"></a>

## Best practices: head node instance type selection<a name="best-practices-head-node-instance-type"></a>

Although the head node doesn't run any job, its functions and its sizing are crucial to the overall performance of the cluster\. When choosing the instance type to use for your head node, you want to evaluate the following items:

**Cluster size:** The head node orchestrates the scaling logic of the cluster and is responsible of attaching new nodes to the scheduler\. If you need to scale up and down the cluster of a considerable amount of nodes, then you want to give the head node some extra compute capacity\.

**Shared file systems:** When using shared file systems to share artifacts between compute nodes and the head node, take into account that the head node is the node exposing the NFS server\. For this reason, you want to choose an instance type with enough network bandwidth and enough dedicated Amazon EBS bandwidth to handle your workflows\.

## Best practices: network performance<a name="best-practices-network-performance-v3"></a>

Network performance is critical to ensuring high performance computing \(HPC\) applications perform as expected\. We recommend these three best practices to optimize your network performance\.
+ **Placement group:** a cluster placement group is a logical grouping of instances within a single Availability Zone\. For more information on placement groups, see [placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\. If you are using Slurm, you can configure each Slurm queue to use a cluster placement group by specifying a [`PlacementGroup`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) in the queue's [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) settings\.

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
+ **Enhanced networking:** Consider choosing an instance type that supports enhanced networking\. This applies to all [current generation instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#current-gen-instances)\. For more information, see [enhanced networking on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Elastic Fabric Adapter:** To support high levels of scaleable inter\-instance communication, consider choosing EFA network interfaces for your network\. The EFA's custom\-built operating system \(OS\) bypass hardware enhances inter\-instance communications with the on\-demand elasticity and flexibility of the AWS cloud\. You can configure each Slurm queue [`ComputeResource`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-ComputeResources) to use [`Efa`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Efa)\. For more information about using EFA with AWS ParallelCluster, see [Elastic Fabric Adapter](efa-v3.md)\.

  ```
  ComputeResources:
    - Name: your-compute-resource-name
      Efa:
        Enabled: true
  ```

  For more information about EFA, see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Instance bandwidth:** The bandwidth scales with instance size\. For guidance on choosing the instance type which best suits your needs, see [Amazon EBS–optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html) and [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Best practices: budget alerts<a name="best-practices-budget-alerts-v3"></a>

To manage AWS ParallelCluster resource costs, we recommend that you use AWS Budgets actions to create a budget and defined budget threshold alerts for selected AWS resources\. For more information, see [Configuring a budget action](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-controls.html) in the *AWS Budgets User Guide*\. You can also use Amazon CloudWatch to create a billing alarm\. For more information, see [Creating a billing alarm to monitor your estimated AWS charges](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html)\.

## Best practices: moving a cluster to a new AWS ParallelCluster minor version<a name="best-practices-cluster-upgrades-v3"></a>

Currently each AWS ParallelCluster minor version is self\-contained along with its `pcluster` CLI\. To move a cluster to a new minor version, you must update the [Cluster configuration file](cluster-configuration-file-v3.md) to the new version and re\-create the cluster using the new version's CLI\.

To optimize the process of moving a cluster to a new minor version, we recommend that you use the following best practices\.
+ Save personal data in external volumes, such as Amazon EFS and FSx for Lustre\. By doing this, you can easily move the data from one cluster to another\.
+ Create shared storage systems of the types listed below using the AWS CLI or AWS Management Console:
  + [`SharedStorage`](SharedStorage-v3.md) / [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-VolumeId)
  + [`SharedStorage`](SharedStorage-v3.md) / [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-FileSystemId)
  + [`SharedStorage`](SharedStorage-v3.md) / [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId)

  Add them to the new cluster configuration as existing file systems\. This way, they are preserved when you delete the cluster and can be attached to a new cluster\.

  We recommend that you use Amazon EFS, or FSx for Lustre file systems because they can be attached to multiple clusters at the same time and you can attach them to the new cluster before deleting the previous one\.
+ Use [custom bootstrap actions](custom-bootstrap-actions-v3.md) to customize your instances rather than a custom AMI\. This optimizes the creation process because a new custom AMI doesn't need to be created for each new version\.
+ Recommended sequence\.

  1. Review and update the cluster configuration to the new version\. Include existing file systems definitions\. Update your bootstrap scripts\.

  1. Verify the `pcluster` version and update it if needed\.

  1. Create and test the new cluster\.
     + Make sure your data is available in the new cluster\.
     + Make sure your application works in the new cluster\.

  1. If you're new cluster is fully tested and operational and you're sure you aren't going to use the old cluster, delete it\.