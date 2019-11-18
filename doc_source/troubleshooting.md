# AWS ParallelCluster Troubleshooting<a name="troubleshooting"></a>

The AWS ParallelCluster community maintains a wiki with many troubleshooting tips at the [aws\-parallelcluster wiki](https://github.com/aws/aws-parallelcluster/wiki/)\.

## Failure submitting AWS Batch multi\-node parallel jobs<a name="troubleshooting-aws-batch-mnp"></a>

If you have problems submitting multi\-node parallel jobs when using AWS Batch as the job scheduler, it's recommend to upgrade to AWS ParallelCluster 2\.5\.0\. If that is not feasible, a workaround can be found at [Self patch a Cluster Used for Submitting Multi node Parallel Jobs through AWS Batch](https://github.com/aws/aws-parallelcluster/wiki/Self-patch-a-Cluster-Used-for-Submitting-Multi-node-Parallel-Jobs-through-AWS-Batch)\.

## Placement Groups and Instance Launch Issues<a name="placement-groups-and-instance-launch-issues"></a>

In order to get the lowest inter\-node latency, AWS recommends that you use a placement group\. A placement group guarantees that your instances will be on the same networking backbone\. If not enough instances are available when the request is made, an `InsufficientInstanceCapacity` error is returned\. To reduce the possibility of receiving an `InsufficientInstanceCapacity` error when using cluster placement groups, set the `[`placement_group`](cluster-definition.md#placement-group)` parameter to `DYNAMIC` and set the `[`placement`](cluster-definition.md#placement)` parameter to `compute`\.

If a high performance shared filesystem is needed, consider using [Amazon FSx for Lustre](http://aws.amazon.com/fsx/lustre/)\.

If the master node must be in the placement group, use the same instance type and subnet for both the master and compute nodes\. In other words, the `[`compute_instance_type`](cluster-definition.md#compute-instance-type)` parameter has the same value as the `[`master_instance_type`](cluster-definition.md#master-instance-type)` parameter, the `[`placement`](cluster-definition.md#placement)` parameter is set to `cluster` and the `[`compute_subnet_id`](vpc-section.md#compute-subnet-id)` parameter is not specified \(the value of the `[`master_subnet_id`](vpc-section.md#master-subnet-id)` parameter is used for the compute notes\.\)

For more information, see [Troubleshooting Instance Launch issues](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html) and [Placement Groups Roles and Limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#concepts-placement-groups) in the *Amazon EC2 User Guide for Linux Instances*