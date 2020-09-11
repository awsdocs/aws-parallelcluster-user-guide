# `[queue]` section<a name="queue-section"></a>

**Topics**
+ [`compute_resource_settings`](#queue-compute-resource-settings)
+ [`compute_type`](#queue-compute-type)
+ [`disable_hyperthreading`](#queue-disable-hyperthreading)
+ [`enable_efa`](#queue-enable-efa)
+ [`placement_group`](#queue-placement-group)

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

## `compute_resource_settings`<a name="queue-compute-resource-settings"></a>

Identifies the [`[compute_resource]` sections](compute-resource-section.md) containing the compute resources configurations for this queue\. The section names must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

Up to three \(3\) [`[compute_resource]` sections](compute-resource-section.md) are supported per [`[queue]` section](#queue-section)\.

For example, the following setting specifies that the sections that start `[compute_resource cr1]` and `[compute_resource cr2]` are used\.

```
compute_resource_settings = cr1, cr2
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `compute_type`<a name="queue-compute-type"></a>

Defines the type of instances to launch for this queue\. This setting replaces the [`cluster_type`](cluster-definition.md#cluster-type) setting\.

Valid options are: `ondemand`, and `spot`\.

The default value is `ondemand`\.

For more information about Spot Instances, see [Working with Spot Instances](spot.md)\.

The following example uses Spot instances for the compute nodes in this queue\.

```
compute_type = spot
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `disable_hyperthreading`<a name="queue-disable-hyperthreading"></a>

Disables hyperthreading on the nodes in this queue\. Not all instance types can disable hyperthreading\. For a list of instance types that support disabling hyperthreading, see [CPU cores and threads per CPU core per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-optimize-cpu.html#cpu-options-supported-instances-values) in the *Amazon EC2 User Guide for Linux Instances*\. If the [`disable_hyperthreading`](cluster-definition.md#disable-hyperthreading) setting in the [`[cluster]` section](cluster-definition.md) is defined, then this setting cannot be defined\.

The default value is `false`\.

```
disable_hyperthreading = true
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `enable_efa`<a name="queue-enable-efa"></a>

If present, specifies that Elastic Fabric Adapter \(EFA\) is enabled for the nodes in this queue\. EFA is supported by the following instance types \(`c5n.18xlarge`, `c5n.metal`, `g4dn.metal`, `i3en.24xlarge`, `i3en.metal`, `m5dn.24xlarge`, `m5n.24xlarge`, `r5dn.24xlarge`, `r5n.24xlarge`, and `p3dn.24xlarge`\) on these operating systems \(`alinux`, `alinux2`, `centos7`, `ubuntu1604`, or `ubuntu1804`\)\. For more information, see [Elastic Fabric Adapter](efa.md)\. If the [`enable_efa`](cluster-definition.md#enable-efa) setting in the [`[cluster]` section](cluster-definition.md) is defined, then this setting cannot be defined\.

```
enable_efa = true
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `placement_group`<a name="queue-placement-group"></a>

\(Optional\) If present, defines the placement group for this queue\. This setting replaces the [`placement_group`](cluster-definition.md#placement-group) setting\.

Valid options are:
+ `DYNAMIC`
+ An existing Amazon EC2 cluster placement group name

When set to `DYNAMIC`, a unique placement group for this queue is created and deleted as part of the cluster stack\.

For more information about placement groups, see [Placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\. If the same placement group is used for different instance types, it’s much more likely that the request will fail with an insufficient capacity error\. For more information, see [Insufficient instance capacity](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html#troubleshooting-launch-capacity) in the *Amazon EC2 User Guide for Linux Instances*\.

Not all instance types support cluster placement groups\. For example, `t2.micro` doesn't support cluster placement groups\. For information about the list of instance types that support cluster placement groups, see [Cluster placement group rules and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-limitations-cluster) in the *Amazon EC2 User Guide for Linux Instances*\. See [Placement groups and instance launch issues](troubleshooting.md#placement-groups-and-instance-launch-issues) for tips when working with placement groups\.

```
placement_group = DYNAMIC
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)