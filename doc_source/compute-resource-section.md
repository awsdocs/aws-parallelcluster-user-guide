# `[compute_resource]` section<a name="compute-resource-section"></a>

**Topics**
+ [`initial_count`](#compute-resource-initial-count)
+ [`instance_type`](#compute-resource-instance-type)
+ [`max_count`](#compute-resource-max-count)
+ [`min_count`](#compute-resource-min-count)
+ [`spot_price`](#compute-resource-spot-price)

Defines configuration settings for a compute resource\. [`[compute_resource]` sections](#compute-resource-section) are referenced by the [`compute_resource_settings`](queue-section.md#queue-compute-resource-settings) setting in the [`[queue]` section](queue-section.md)\. [`[compute_resource]` sections](#compute-resource-section) are only supported when [`scheduler`](cluster-definition.md#scheduler) is set to `slurm`\.

The format is `[compute_resource <compute-resource-name>]`\. *compute\-resource\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[compute_resource cr1]
instance_type = c5.xlarge
min_count = 0
initial_count = 2
max_count = 10
spot_price = 0.5
```

**Note**  
Support for the [`[compute_resource]` section](#compute-resource-section) was added in AWS ParallelCluster version 2\.9\.0\.

## `initial_count`<a name="compute-resource-initial-count"></a>

**\(Optional\)** Sets the initial number of Amazon EC2 instances to launch for this compute resource\. Cluster creation doesn't complete until at least this many nodes have been launched into the compute resource\. If the [`compute_type`](queue-section.md#queue-compute-type) setting for the queue is `spot` and there aren't enough Spot Instances available, the cluster creation might time out and fail\. Any count larger than the [`min_count`](#compute-resource-min-count) setting is dynamic capacity subject to the [`scaledown_idletime`](scaling-section.md#scaledown-idletime) setting\. This setting replaces the [`initial_queue_size`](cluster-definition.md#configuration-initial-queue-size) setting\.

Defaults to `0`\.

```
initial_count = 2
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `instance_type`<a name="compute-resource-instance-type"></a>

**\(Required\)** Defines the Amazon EC2 instance type that's used for this compute resource\. The architecture of the instance type must be the same as the architecture used for the [`master_instance_type`](cluster-definition.md#master-instance-type) setting\. The `instance_type` setting must be unique for each [`[compute_resource]` section](#compute-resource-section) referenced by a [`[queue]` section](queue-section.md)\. This setting replaces the [`compute_instance_type`](cluster-definition.md#compute-instance-type) setting\.

```
instance_type = t2.micro
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `max_count`<a name="compute-resource-max-count"></a>

**\(Optional\)** Sets the maximum number of Amazon EC2 instances that can be launched in this compute resource\. Any count larger than the [`initial_count`](#compute-resource-initial-count) setting is started in a power down mode\. This setting replaces the [`max_queue_size`](cluster-definition.md#configuration-max-queue-size) setting\.

Defaults to `10`\.

```
max_count = 10
```

[Update policy: Reducing the size of a queue below the current number of nodes requires that the compute fleet be stopped first.](using-pcluster-update.md#update-policy-max-count)

**Note**  
The update policy did not support changing the `max_count` setting until the compute fleet was stopped for AWS ParallelCluster version 2\.0\.0 through version 2\.9\.1\.

## `min_count`<a name="compute-resource-min-count"></a>

**\(Optional\)** Sets the minimum number of Amazon EC2 instances that can be launched in this compute resource\. These nodes are all static capacity\. Cluster creation doesn't complete until at least this number of nodes has been launched into the compute resource\.

Defaults to `0`\.

```
min_count = 1
```

[Update policy: Reducing the number of static nodes in a queue requires that the compute fleet be stopped first.](using-pcluster-update.md#update-policy-min-count)

**Note**  
The update policy did not support changing the `min_count` setting until the compute fleet was stopped for AWS ParallelCluster version 2\.0\.0 through version 2\.9\.1\.

## `spot_price`<a name="compute-resource-spot-price"></a>

**\(Optional\)** Sets the maximum Spot price for this compute resource\. Used only when the [`compute_type`](queue-section.md#queue-compute-type) setting for the queue containing this compute resources is set to `spot`\. This setting replaces the [`spot_price`](cluster-definition.md#spot-price) setting\.

If you don't specify a value, you're charged the Spot price, capped at the On\-Demand price\.

For assistance finding a Spot Instance that meets your needs, see the [Spot Instance advisor](https://aws.amazon.com/ec2/spot/instance-advisor/)\.

```
spot_price = 1.50
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)