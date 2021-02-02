# Intel Select Solutions<a name="intel-select-solutions"></a>

AWS ParallelCluster is available as an Intel Select Solution for simulation and modeling\. Configurations are verified to meet the standards set by the [Intel HPC Platform Specification](https://www.intel.com/content/www/us/en/high-performance-computing/hpc-platform-specification.html), use specific Intel instance types, and are configured to use the [Elastic Fabric Adapter](efa.md) \(EFA\) networking interface\. AWS ParallelCluster is the first cloud solution to meet the requirements for the Intel Select Solutions program\. Supported instance types include `c5n.18xlarge`, `m5n.24xlarge`, and `r5n.24xlarge`\. A sample configuration compatible with the Intel Select Solutions standard is provided below\.

**Example Intel Select Solutions configuration**  

```
[global]
update_check = true
sanity_check = true
cluster_template = intel-select-solutions

[aws]
aws_region_name = <Your AWS Region>

[scaling demo]
scaledown_idletime = 5

[cluster intel-select-solutions]
key_name = <Your SSH key name>
base_os = centos7
scheduler = slurm
enable_intel_hpc_platform = true
master_instance_type = c5.xlarge
vpc_settings = <Your VPC section>
scaling_settings = demo
queue_settings = c5n,m5n,r5n
master_root_volume_size = 200
compute_root_volume_size = 80

[queue c5n]
compute_resource_settings = c5n_i1
enable_efa = true
placement_group = DYNAMIC

[compute_resource c5n_i1]
instance_type = c5n.18xlarge
max_count = 5

[queue m5n]
compute_resource_settings = m5n_i1
enable_efa = true
placement_group = DYNAMIC

[compute_resource m5n_i1]
instance_type = m5n.24xlarge
max_count = 5

[queue r5n]
compute_resource_settings = r5n_i1
enable_efa = true
placement_group = DYNAMIC

[compute_resource r5n_i1]
instance_type = r5n.24xlarge
max_count = 5
```

For more information about AWS ParallelCluster and the Intel HPC Platform Specification, see [Intel HPC Platform Specification](intel-hpc-platform-specification.md)\.