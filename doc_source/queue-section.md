# `[queue]` section<a name="queue-section"></a>

Defines configuration settings for a single queue\. [`[queue]` sections](#queue-section) are only supported when [`scheduler`](cluster-definition.md#scheduler) is set to `slurm`\.

The format is `[queue <queue-name>]`\. *queue\-name* must start with a lowercase letter, contain no more than 30 characters, and only contain lowercase letters, numbers, and hyphens \(\-\)\.

```
[queue q1]
compute_resource_settings = i1,i2
placement_group = DYNAMIC
enable_efa = true
disable_hyperthreading = false
compute_type = spot
```

**Note**  
Support for the [`[queue]` section](#queue-section) was added in AWS ParallelCluster version 2\.9\.0\.

**Topics**
+ [`compute_resource_settings`](#queue-compute-resource-settings)
+ [`compute_type`](#queue-compute-type)
+ [`disable_hyperthreading`](#queue-disable-hyperthreading)
+ [`enable_efa`](#queue-enable-efa)
+ [`enable_efa_gdr`](#queue-enable-efa-gdr)
+ [`placement_group`](#queue-placement-group)

## `compute_resource_settings`<a name="queue-compute-resource-settings"></a>

**\(Required\)** Identifies the [`[compute_resource]` sections](compute-resource-section.md) containing the compute resources configurations for this queue\. The section names must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

Up to three \(3\) [`[compute_resource]` sections](compute-resource-section.md) are supported for each [`[queue]` section](#queue-section)\.

For example, the following setting specifies that the sections that start `[compute_resource cr1]` and `[compute_resource cr2]` are used\.

```
compute_resource_settings = cr1, cr2
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `compute_type`<a name="queue-compute-type"></a>

**\(Optional\)** Defines the type of instances to launch for this queue\. This setting replaces the [`cluster_type`](cluster-definition.md#cluster-type) setting\.

Valid options are: `ondemand`, and `spot`\.

The default value is `ondemand`\.

For more information about Spot Instances, see [Working with Spot Instances](spot.md)\.

**Note**  
Using Spot Instances requires that the `AWSServiceRoleForEC2Spot` service\-linked role exist in your account\. To create this role in your account using the AWS CLI, run the following command:  

```
aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
```
For more information, see [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.

The following example uses SpotInstances for the compute nodes in this queue\.

```
compute_type = spot
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `disable_hyperthreading`<a name="queue-disable-hyperthreading"></a>

**\(Optional\)** Disables hyperthreading on the nodes in this queue\. Not all instance types can disable hyperthreading\. For a list of instance types that support disabling hyperthreading, see [CPU cores and threads for each CPU core per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-optimize-cpu.html#cpu-options-supported-instances-values) in the *Amazon EC2 User Guide for Linux Instances*\. If the [`disable_hyperthreading`](cluster-definition.md#disable-hyperthreading) setting in the [`[cluster]` section](cluster-definition.md) is defined, then this setting cannot be defined\.

The default value is `false`\.

```
disable_hyperthreading = true
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `enable_efa`<a name="queue-enable-efa"></a>

**\(Optional\)** If set to `true`, specifies that Elastic Fabric Adapter \(EFA\) is enabled for the nodes in this queue\. To view the list of EC2 instances that support EFA, see [Supported instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html#efa-instance-types) in the *Amazon EC2 User Guide for Linux Instances*\. If the [`enable_efa`](cluster-definition.md#enable-efa) setting in the [`[cluster]` section](cluster-definition.md) is defined, then this setting cannot be defined\. A cluster placement group should be used to minimize latencies between instances\. For more information, see [`placement`](cluster-definition.md#placement) and [`placement_group`](cluster-definition.md#placement-group)\.

```
enable_efa = true
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `enable_efa_gdr`<a name="queue-enable-efa-gdr"></a>

**\(Optional\)** Starting with AWS ParallelCluster version 2\.11\.3, this setting has no effect\. Elastic Fabric Adapter \(EFA\) support for GPUDirect RDMA \(remote direct memory access\) is enabled for the compute nodes is always enabled if it's supported by the instance type\.

**Note**  
AWS ParallelCluster version 2\.10\.0 through 2\.11\.2: If `true`, specifies that Elastic Fabric Adapter \(EFA\) GPUDirect RDMA \(remote direct memory access\) is enabled for the nodes in this queue\. Setting this to `true` requires that the [`enable_efa`](#queue-enable-efa) setting is set to `true` \.EFA GPUDirect RDMA is supported by the following instance types \(`p4d.24xlarge`\) on these operating systems \(`alinux2`, `centos7`, `ubuntu1804`, or `ubuntu2004`\)\. If the [`enable_efa_gdr`](cluster-definition.md#enable-efa-gdr) setting in the [`[cluster]` section](cluster-definition.md) is defined, then this setting cannot be defined\. A cluster placement group should be used to minimize latencies between instances\. For more information, see [`placement`](cluster-definition.md#placement) and [`placement_group`](cluster-definition.md#placement-group)\.

The default value is `false`\.

```
enable_efa_gdr = true
```

**Note**  
Support for `enable_efa_gdr` was added in AWS ParallelCluster version 2\.10\.0\.

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `placement_group`<a name="queue-placement-group"></a>

**\(Optional\)** If present, defines the placement group for this queue\. This setting replaces the [`placement_group`](cluster-definition.md#placement-group) setting\.

Valid options are the following values:
+ `DYNAMIC`
+ An existing Amazon EC2 cluster placement group name

When set to `DYNAMIC`, a unique placement group for this queue is created and deleted as part of the cluster stack\.

For more information about placement groups, see [Placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\. If the same placement group is used for different instance types, it’s more likely that the request might fail due to an insufficient capacity error\. For more information, see [Insufficient instance capacity](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html#troubleshooting-launch-capacity) in the *Amazon EC2 User Guide for Linux Instances*\.

There is no default value\.

Not all instance types support cluster placement groups\. For example, `t2.micro` doesn't support cluster placement groups\. For information about the list of instance types that support cluster placement groups, see [Cluster placement group rules and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-limitations-cluster) in the *Amazon EC2 User Guide for Linux Instances*\. See [Placement groups and instance launch issues](troubleshooting.md#placement-groups-and-instance-launch-issues) for tips when working with placement groups\.

```
placement_group = DYNAMIC
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)