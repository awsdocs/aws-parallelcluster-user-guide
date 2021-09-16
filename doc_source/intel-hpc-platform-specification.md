# Intel HPC Platform Specification<a name="intel-hpc-platform-specification"></a>

AWS ParallelCluster is compliant with the Intel HPC Platform Specification\. The Intel HPC Platform Specification provides a set of compute, fabric, memory, storage, and software requirements to help achieve a high standard of quality and compatibility with HPC workloads\. For more information, see [Intel HPC Platform Specification](https://www.intel.com/content/www/us/en/high-performance-computing/hpc-platform-specification.html) and [Applications Verified Compatible with the Intel HPC Platform Specification](https://www.intel.com/content/www/us/en/high-performance-computing/hpc-application-catalog.html)\.

To be compliant with the Intel HPC Platform Specification, the following requirements must be met:
+ The operating system must be CentOS 7 \([`base_os`](cluster-definition.md#base-os)` = centos7`\)\.
+ The instance type for the compute nodes must have an Intel CPU and at least 64 GB of memory\. For the `c5` family of instance types, this means that the instance type must be at least a `c5.9xlarge` \([`compute_instance_type`](cluster-definition.md#compute-instance-type)` = c5.9xlarge`\)\.
+ The head node must have at least 200 GB of storage\.
+ The End User License Agreement for Intel Parallel Studio must be accepted \([`enable_intel_hpc_platform`](cluster-definition.md#enable-intel-hpc-platform)` = true`\)\.
+ Each compute node must have at least 80 GB of storage \([`compute_root_volume_size`](cluster-definition.md#compute-root-volume-size)` = 80`\)\.

The storage can be local or on a network \(NFS shared from the head node, Amazon EBS or FSx for Lustre\), and it can be shared\.