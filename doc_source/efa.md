# Elastic Fabric Adapter<a name="efa"></a>

Elastic Fabric Adapter \(EFA\) is a network device that has OS\-bypass capabilities for low\-latency network communications with other instances on the same subnet\. EFA is exposed by using Libfabric, and can be used by applications using the Messaging Passing Interface \(MPI\)\.

To use EFA with AWS ParallelCluster, add the line `enable_efa = true` to the [`[queue]` section](queue-section.md)\.

To view the list of EC2 instances that support EFA, see [Supported instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html#efa-instance-types) in the *Amazon EC2 User Guide for Linux Instances*\.

For more information about the `enable_efa` setting, see [`enable_efa`](queue-section.md#queue-enable-efa) in the [`[queue]` section](queue-section.md)\.

A cluster placement group should be used to minimize latencies between instances\. For more information, see [`placement`](cluster-definition.md#placement) and [`placement_group`](cluster-definition.md#placement-group)\.

For more information, see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) in the *Amazon EC2 User Guide for Linux Instances* and [Scale HPC workloads with elastic fabric adapter and AWS ParallelCluster](https://aws.amazon.com/blogs/opensource/scale-hpc-workloads-elastic-fabric-adapter-and-aws-parallelcluster/) in the *AWS Open Source Blog*\.

**Note**  
By default, Ubuntu distributions enable ptrace \(process trace\) protection\. Starting with AWS ParallelCluster 2,6\.0, ptrace protection is disabled so that Libfabric works properly\. For more information, see [Disable ptrace protection](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa-start.html#efa-start-ptrace) in the *Amazon EC2 User Guide for Linux Instances*\.

**Note**  
Support EFA on Arm\-based Graviton2 instances was added in AWS ParallelCluster version 2\.10\.1\.