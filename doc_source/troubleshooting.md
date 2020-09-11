# AWS ParallelCluster troubleshooting<a name="troubleshooting"></a>

The AWS ParallelCluster community maintains a Wiki with many troubleshooting tips at the [aws\-parallelcluster wiki](https://github.com/aws/aws-parallelcluster/wiki/)\.

## Failure submitting AWS Batch multi\-node parallel jobs<a name="troubleshooting-aws-batch-mnp"></a>

If you have problems submitting multi\-node parallel jobs when using AWS Batch as the job scheduler, we recommend that you upgrade to AWS ParallelCluster version 2\.5\.0\. If that is not feasible, you can use a workaround\. For information about this workaround, see [Self patch a cluster used for submitting multi node parallel jobs through AWS Batch](https://github.com/aws/aws-parallelcluster/wiki/Self-patch-a-Cluster-Used-for-Submitting-Multi-node-Parallel-Jobs-through-AWS-Batch)\.

## Placement groups and instance launch issues<a name="placement-groups-and-instance-launch-issues"></a>

To get the lowest inter\-node latency, we recommend that you use a *placement group*\. A placement group guarantees that your instances are on the same networking backbone\. If not enough instances are available when the request is made, an `InsufficientInstanceCapacity` error is returned\. To reduce the possibility of receiving an `InsufficientInstanceCapacity` error when using cluster placement groups, set the [`placement_group`](cluster-definition.md#placement-group) parameter to `DYNAMIC` and set the [`placement`](cluster-definition.md#placement) parameter to `compute`\.

If you need a high performance shared filesystem, consider using [Amazon FSx for Lustre](http://aws.amazon.com/fsx/lustre/)\.

If the head node must be in the placement group, use the same instance type and subnet for the head and all compute nodes\. As such, the [`compute_instance_type`](cluster-definition.md#compute-instance-type) parameter has the same value as the [`master_instance_type`](cluster-definition.md#master-instance-type) parameter, the [`placement`](cluster-definition.md#placement) parameter is set to `cluster`, and the [`compute_subnet_id`](vpc-section.md#compute-subnet-id) parameter is not specified\. With this configuration, the value of the [`master_subnet_id`](vpc-section.md#master-subnet-id) parameter is used for the compute nodes\.

For more information, see [Troubleshooting instance launch issues](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html) and [Placement groups roles and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#concepts-placement-groups) in the *Amazon EC2 User Guide for Linux Instances*

## Directories that cannot be replaced<a name="directories-cannot-be-replaced"></a>

The following directories are shared between the nodes and cannot be replaced\.

`/home`  
This includes the default user home folder \(`/home/ec2_user` on Amazon Linux, `/home/centos` on CentOS, and `/home/ubuntu` on Ubuntu\.\)

`/opt/intel`  
This includes Intel MPI, Intel Parallel Studio, and related files\.

`/opt/sge`  
This includes Son of Grid Engine and related files\. \(Conditional, only if [`scheduler`](cluster-definition.md#scheduler)` = sge`\.\)

`/opt/slurm`  
This includes Slurm Workload Manager and related files\. \(Conditional, only if [`scheduler`](cluster-definition.md#scheduler)` = slurm`\.\)

`/opt/torque`  
This includes Torque Resource Manager and related files\. \(Conditional, only if [`scheduler`](cluster-definition.md#scheduler)` = torque`\.\)

## NICE DCV troubleshooting<a name="nice-dcv-troubleshooting"></a>

The logs for NICE DCV are written to files in the `/var/log/dcv/` directory\. Reviewing these logs can help to troubleshoot problems\.

The instance type should have at least 1\.7 GiB of RAM to run NICE DCV\. Nano and micro instance types don't have enough memory to run NICE DCV\.