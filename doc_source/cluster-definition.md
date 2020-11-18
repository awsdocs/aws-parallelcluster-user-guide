# `[cluster]` section<a name="cluster-definition"></a>

**Topics**
+ [`additional_cfn_template`](#additional-cfn-template)
+ [`additional_iam_policies`](#additional-iam-policies)
+ [`base_os`](#base-os)
+ [`cluster_type`](#cluster-type)
+ [`compute_instance_type`](#compute-instance-type)
+ [`compute_root_volume_size`](#compute-root-volume-size)
+ [`custom_ami`](#custom-ami-section)
+ [`cluster_resource_bucket`](#cluster-resource-bucket-section)
+ [`cw_log_settings`](#cw-log-settings)
+ [`dashboard_settings`](#dashboard-settings)
+ [`disable_cluster_dns`](#disable-cluster-dns-settings)
+ [`dcv_settings`](#dcv-settings)
+ [`desired_vcpus`](#desired-vcpus)
+ [`disable_hyperthreading`](#disable-hyperthreading)
+ [`ebs_settings`](#ebs-settings)
+ [`ec2_iam_role`](#ec2-iam-role)
+ [`efs_settings`](#efs-settings)
+ [`enable_efa`](#enable-efa)
+ [`enable_efa_gdr`](#enable-efa-gdr)
+ [`enable_intel_hpc_platform`](#enable-intel-hpc-platform)
+ [`encrypted_ephemeral`](#encrypted-ephemeral)
+ [`ephemeral_dir`](#ephemeral-dir)
+ [`extra_json`](#extra-json)
+ [`fsx_settings`](#fsx-settings)
+ [`initial_queue_size`](#configuration-initial-queue-size)
+ [`key_name`](#key-name)
+ [`maintain_initial_size`](#maintain-initial-size)
+ [`master_instance_type`](#master-instance-type)
+ [`master_root_volume_size`](#master-root-volume-size)
+ [`max_queue_size`](#configuration-max-queue-size)
+ [`max_vcpus`](#max-vcpus)
+ [`min_vcpus`](#min-vcpus)
+ [`placement`](#placement)
+ [`placement_group`](#placement-group)
+ [`post_install`](#post-install)
+ [`post_install_args`](#post-install-args)
+ [`pre_install`](#pre-install)
+ [`pre_install_args`](#pre-install-args)
+ [`proxy_server`](#proxy-server)
+ [`queue_settings`](#queue-settings)
+ [`raid_settings`](#raid-settings)
+ [`s3_read_resource`](#s3-read-resource)
+ [`s3_read_write_resource`](#s3-read-write-resource)
+ [`scaling_settings`](#scaling-settings)
+ [`scheduler`](#scheduler)
+ [`shared_dir`](#cluster-shared-dir)
+ [`spot_bid_percentage`](#spot-bid-percentage)
+ [`spot_price`](#spot-price)
+ [`tags`](#tags)
+ [`template_url`](#template-url)
+ [`vpc_settings`](#vpc-settings)

Defines a cluster template that can be used to create a cluster\. A config file can contain multiple `[cluster]` sections\.

The same cluster template can be used to create multiple clusters\.

The format is `[cluster cluster-template-name]`\. The [`[cluster]` section](#cluster-definition) named by the [`cluster_template`](global.md#cluster-template) setting in the [`[global]` section](global.md) is used by default, but can be overridden on the [`pcluster`](pcluster.md) command line\.

*cluster\-template\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[cluster default]
```

## `additional_cfn_template`<a name="additional-cfn-template"></a>

**\(Optional\)** Defines an additional AWS CloudFormation template to launch along with the cluster\. This additional template is used for creating resources that are outside of the cluster but are part of the cluster's lifecycle\.

When set to a value other than `NONE`, the value must be an HTTP URL to a public template, with all parameters provided\.

The default value is `NONE`\.

```
additional_cfn_template = NONE
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `additional_iam_policies`<a name="additional-iam-policies"></a>

**\(Optional\)** Specifies a list of Amazon Resource Names \(ARNs\) of IAM policies for Amazon EC2\. This list is attached to the root role used in the cluster in addition to the permissions required by AWS ParallelCluster separated by commas\. An IAM policy name and its ARN are different\. Names can't be used as an argument to [`additional_iam_policies`](#additional-iam-policies)\. [`additional_iam_policies`](#additional-iam-policies) should be used instead of the [`ec2_iam_role`](#ec2-iam-role)\. This is because [`additional_iam_policies`](#additional-iam-policies) are added to the permissions that AWS ParallelCluster requires, and the [`ec2_iam_role`](#ec2-iam-role) must include all permissions required\. The permissions required often change from release to release as features are added\.

The default value is `NONE`\.

```
additional_iam_policies = arn:aws:iam::aws:policy/AdministratorAccess
```

**Note**  
Support for [`additional_iam_policies`](#additional-iam-policies) was added in AWS ParallelCluster version 2\.5\.0\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `base_os`<a name="base-os"></a>

**\(Required\)** Specifies which OS type is used in the cluster\.

Available options are:
+ `alinux`
+ `alinux2`
+ `centos7`
+ `centos8`
+ `ubuntu1604`
+ `ubuntu1804`

**Note**  
For AWS Graviton\-based instances, only `alinux2` or `ubuntu1804` are supported\.

**Note**  
Support for `centos8` was added and support for `centos6` was removed in AWS ParallelCluster version 2\.10\.0\. Support for `alinux2` was added in AWS ParallelCluster version 2\.6\.0\. Support for `ubuntu1804` was added, and support for `ubuntu1404` was removed in AWS ParallelCluster version 2\.5\.0\.

Other than the specific Regions mentioned in the following table that don't support `centos7` and `centos8`\. All other AWS commercial Regions support all of the following operating systems\. 


| Partition \(Regions\) | `alinux` and `alinux2` | `centos7` and `centos8` | `ubuntu1604` and `ubuntu1804` | 
| --- | --- | --- | --- | 
| Commercial \(All Regions not specifically mentioned\) | True | True | True | 
| AWS GovCloud \(US\-East\) \(us\-gov\-east\-1\) | True | False | True | 
| AWS GovCloud \(US\-West\) \(us\-gov\-west\-1\) | True | False | True | 
| China \(Beijing\) \(cn\-north\-1\) | True | False | True | 
| China \(Ningxia\) \(cn\-northwest\-1\) | True | False | True | 

**Note**  
The [`base_os`](#base-os) parameter also determines the user name that is used to log into the cluster\.
+ `centos7` and `centos8`: `centos` 
+ `ubuntu1604` and `ubuntu1804`: `ubuntu` 
+ `alinux` and `alinux2`: `ec2-user` 

**Note**  
Before AWS ParallelCluster version 2\.7\.0, the [`base_os`](#base-os) parameter was optional, and the default was `alinux`\. Starting with AWS ParallelCluster version 2\.7\.0, the [`base_os`](#base-os) parameter is required\.

**Note**  
If the [`scheduler`](#scheduler) parameter is `awsbatch`, either `alinux` or `alinux2` is supported\.

```
base_os = alinux
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `cluster_type`<a name="cluster-type"></a>

**\(Optional\)** Defines the type of cluster to launch\. If the [`queue_settings`](#queue-settings) setting is defined, then this setting must be replaced by the [`compute_type`](queue-section.md#queue-compute-type) settings in the [`[queue]` sections](queue-section.md)\.

Valid options are: `ondemand`, and `spot`\.

The default value is `ondemand`\.

For more information about Spot Instances, see [Working with Spot Instances](spot.md)\.

```
cluster_type = ondemand
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `compute_instance_type`<a name="compute-instance-type"></a>

**\(Optional\)** Defines the Amazon EC2 instance type that is used for the cluster compute nodes\. The architecture of the instance type must be the same as the architecture used for the [`master_instance_type`](#master-instance-type) setting\. If the [`queue_settings`](#queue-settings) setting is defined, then this setting must be replaced by the [`instance_type`](compute-resource-section.md#compute-resource-instance-type) settings in the [`[compute_resource]` sections](compute-resource-section.md)\.

If you're using the `awsbatch` scheduler, see the Compute Environments creation in the AWS Batch UI for a list of supported instance types\.

Defaults to `t2.micro`, `optimal` when the scheduler is `awsbatch`\.

```
compute_instance_type = t2.micro
```

**Note**  
Support for AWS Graviton\-based instances \(including `A1` and `C6g` instances\) was added in AWS ParallelCluster version 2\.8\.0\.

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `compute_root_volume_size`<a name="compute-root-volume-size"></a>

**\(Optional\)** Specifies the ComputeFleet root volume size in GB\. The AMI must support growroot\.

The default value is `25`\.

**Note**  
Before AWS ParallelCluster version 2\.5\.0, the default was 20\.

```
compute_root_volume_size = 20
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `custom_ami`<a name="custom-ami-section"></a>

**\(Optional\)** Specifies the ID of a custom AMI to use for the head and compute nodes instead of the default [published AMIs](https://github.com/aws/aws-parallelcluster/blob/v2.10/amis.txt)\.

The default value is `NONE`\.

```
custom_ami = NONE
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `cluster_resource_bucket`<a name="cluster-resource-bucket-section"></a>

**\(Optional\)** Specifies the name of the Amazon S3 bucket that is used to host resources that are generated when the cluster is created\. The bucket must have versioning enabled\. For more information, see [Using versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) in the *Amazon Simple Storage Service Developer Guide*\. This bucket can be used for multiple clusters\.

If this parameter isn't specified, a new bucket is created when the cluster is created\. The new bucket has the name of `parallelcluster-random_string`\. In this name, *random\_string* is a random string of alphanumeric characters\. All cluster resources are stored in this bucket in a path with the form `bucket_name/resource_directory`\. `resource_directory` has the form `stack_name-random_string`, where *stack\_name* is the name of one of the AWS CloudFormation stacks used by AWS ParallelCluster\. The value of *bucket\_name* can be found in the `ResourcesS3Bucket` value in the output of the `parallelcluster-clustername` stack\. The value of *resource\_directory* can be found in the value of the `ArtifactS3RootDirectory` output from the same stack\.

The default value is `parallelcluster-random_string`\.

```
cluster_resource_bucket = my-s3-bucket
```

**Note**  
Support for [`cluster_resource_bucket`](#cluster-resource-bucket-section) was added in AWS ParallelCluster version 2\.10\.0\.

[Update policy: If this setting is changed, the update is not allowed. Updating this setting cannot be forced.](using-pcluster-update.md#update-policy-read-only-resource-bucket)

## `cw_log_settings`<a name="cw-log-settings"></a>

**\(Optional\)** Identifies the `[cw_log]` section with the CloudWatch Logs configuration\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[cw_log]` section](cw-log-section.md), [Amazon CloudWatch dashboard](cloudwatch-dashboard.md), and [Integration with Amazon CloudWatch Logs](cloudwatch-logs.md)\.

For example, the following setting specifies that the section that starts `[cw_log custom-cw]` is used for the CloudWatch Logs configuration\.

```
cw_log_settings = custom-cw
```

**Note**  
Support for [`cw_log_settings`](#cw-log-settings) was added in AWS ParallelCluster version 2\.6\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `dashboard_settings`<a name="dashboard-settings"></a>

**\(Optional\)** Identifies the `[dashboard]` section with the CloudWatch dashboard configuration\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[dashboard]` section](dashboard-section.md)\.

For example, the following setting specifies that the section that starts `[dashboard custom-dashboard]` is used for the CloudWatch dashboard configuration\.

```
dashboard_settings = custom-dashboard
```

**Note**  
Support for [`dashboard_settings`](#dashboard-settings) was added in AWS ParallelCluster version 2\.10\.0\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `disable_cluster_dns`<a name="disable-cluster-dns-settings"></a>

**\(Optional\)** Specifies if the DNS entries for the cluster shouldn't be created\. By default, AWS ParallelCluster creates a Route 53 hosted zone\. If `disable_cluster_dns` is set to `true`, the hosted zone isn't created\.

The default value is `false`\.

```
disable_cluster_dns = true
```

**Note**  
Support for [`disable_cluster_dns`](#disable-cluster-dns-settings) was added in AWS ParallelCluster version 2\.9\.1\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `dcv_settings`<a name="dcv-settings"></a>

**\(Optional\)** Identifies the `[dcv]` section with the NICE DCV configuration\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[dcv]` section](dcv-section.md)\.

For example, the following setting specifies that the section that starts `[dcv custom-dcv]` is used for the NICE DCV configuration\.

```
dcv_settings = custom-dcv
```

**Note**  
On AWS Graviton\-based instances, NICE DCV is only supported on `alinux2`\.

**Note**  
Support for [`dcv_settings`](#dcv-settings) was added in AWS ParallelCluster version 2\.5\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `desired_vcpus`<a name="desired-vcpus"></a>

**\(Optional\)** Specifies the desired number of vCPUs in the compute environment\. Used only if the scheduler is `awsbatch`\.

The default value is `4`\.

```
desired_vcpus = 4
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)

## `disable_hyperthreading`<a name="disable-hyperthreading"></a>

**\(Optional\)** Disables hyperthreading on the head and compute nodes\. Not all instance types can disable hyperthreading\. For a list of instance types that support disabling hyperthreading, see [CPU cores and threads per CPU core per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-optimize-cpu.html#cpu-options-supported-instances-values) in the *Amazon EC2 User Guide for Linux Instances*\. If the [`queue_settings`](#queue-settings) setting is defined, either this setting can be defined, or the [`disable_hyperthreading`](queue-section.md#queue-disable-hyperthreading) settings in the [`[queue]` sections](queue-section.md) can be defined\.

The default value is `false`\.

```
disable_hyperthreading = true
```

**Note**  
Support for [`disable_hyperthreading`](#disable-hyperthreading) was added in AWS ParallelCluster version 2\.5\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_settings`<a name="ebs-settings"></a>

**\(Optional\)** Identifies the `[ebs]` sections with the Amazon EBS volumes that are mounted on the head node\. When using multiple Amazon EBS volumes, enter these parameters in a list with each one separated by a comma\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

Up to five \(5\) additional Amazon EBS volumes are supported\.

For more information, see the [`[ebs]` section](ebs-section.md)\.

For example, the following setting specifies that the sections that start `[ebs custom1]` and `[ebs custom2]` are used for the Amazon EBS volumes\.

```
ebs_settings = custom1, custom2
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ec2_iam_role`<a name="ec2-iam-role"></a>

**\(Optional\)** Defines the name of an existing IAM role for Amazon EC2 that is attached to all instances in the cluster\. An IAM role name and its Amazon Resource Name \(ARN\) are different\. ARNscan;t be used as an argument to [`ec2_iam_role`](#ec2-iam-role)\. If this option is specified, the [`additional_iam_policies`](#additional-iam-policies) setting is ignored\. We recommend that you use [`additional_iam_policies`](#additional-iam-policies), rather than [`ec2_iam_role`](#ec2-iam-role), because features added to AWS ParallelCluster often require new permissions\.

The default value is `NONE`\.

```
ec2_iam_role = NONE
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `efs_settings`<a name="efs-settings"></a>

**\(Optional\)** Specifies settings related to the Amazon EFS filesystem\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[efs]` section](efs-section.md)\.

For example, the following setting specifies that the section that starts `[efs customfs]` is used for the Amazon EFS filesystem configuration\.

```
efs_settings = customfs
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `enable_efa`<a name="enable-efa"></a>

**\(Optional\)** If present, specifies that Elastic Fabric Adapter \(EFA\) is enabled for the compute nodes\. EFA is supported by specific instance types \(`c5n.18xlarge`, `c5n.metal`, `g4dn.metal`, `i3en.24xlarge`, `i3en.metal`, `m5dn.24xlarge`, `m5n.24xlarge`, `r5dn.24xlarge`, `r5n.24xlarge`, and `p3dn.24xlarge`\) on specific operating systems \([`base_os`](#base-os) is `alinux`, `alinux2`, `centos7`, `centos8`, `ubuntu1604`, or `ubuntu1804`\)\. For more information, see [Elastic Fabric Adapter](efa.md)\. If the [`queue_settings`](#queue-settings) setting is defined, either this setting can be defined, or the [`enable_efa`](queue-section.md#queue-enable-efa) settings in the [`[queue]` sections](queue-section.md) can be defined\.

```
enable_efa = compute
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `enable_efa_gdr`<a name="enable-efa-gdr"></a>

**\(Optional\)** If `compute`, specifies that Elastic Fabric Adapter \(EFA\) support for GPUDirect RDMA \(remote direct memory access\) is enabled for the compute nodes\. Setting this setting to `compute` requires that the [`enable_efa`](#enable-efa) setting is set to `compute`\. EFA support for GPUDirect RDMA is supported by specific instance types \(`p4d.24xlarge`\) on specific operating systems \([`base_os`](#base-os) is `alinux`, `alinux2`, `centos7`, `centos8`, `ubuntu1604`, or `ubuntu1804`\)\. If the [`queue_settings`](#queue-settings) setting is defined, either this setting can be defined, or the [`enable_efa_gdr`](queue-section.md#queue-enable-efa-gdr) settings in the [`[queue]` sections](queue-section.md) can be defined\.

```
enable_efa_gdr = compute
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `enable_intel_hpc_platform`<a name="enable-intel-hpc-platform"></a>

**\(Optional\)** If present, indicates that the [End user license agreement](https://software.intel.com/en-us/articles/end-user-license-agreement) for Intel Parallel Studio is accepted\. This causes Intel Parallel Studio to be installed on the head node and shared with the compute nodes\. This adds several minutes to the time it takes the head node to bootstrap\. The [`enable_intel_hpc_platform`](#enable-intel-hpc-platform) setting is only supported on CentOS 7 \([`base_os`](#base-os)` = centos7`\) and CentOS 8 \([`base_os`](#base-os)` = centos8`\)\.

The default value is `false`\.

```
enable_intel_hpc_platform = true
```

**Note**  
The [`enable_intel_hpc_platform`](#enable-intel-hpc-platform) parameter is not compatible with AWS Graviton\-based instances\.

**Note**  
Support for [`enable_intel_hpc_platform`](#enable-intel-hpc-platform) was added in AWS ParallelCluster version 2\.5\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `encrypted_ephemeral`<a name="encrypted-ephemeral"></a>

**\(Optional\)** Encrypts the ephemeral instance store volumes with non\-recoverable in\-memory keys, using LUKS \(Linux Unified Key Setup\)\.

For more information, see [https://gitlab.com/cryptsetup/cryptsetup/blob/master/README.md](https://gitlab.com/cryptsetup/cryptsetup/blob/master/README.md)\.

The default value is `false`\.

```
encrypted_ephemeral = true
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ephemeral_dir`<a name="ephemeral-dir"></a>

**\(Optional\)** Defines the path where instance store volumes are mounted if they are used\.

The default value is `/scratch`\.

```
ephemeral_dir = /scratch
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `extra_json`<a name="extra-json"></a>

**\(Optional\)** Defines the extra JSON that is merged into the Chef `dna.json`\. For more information, see [Building a Custom AWS ParallelCluster AMI](tutorials_02_ami_customization.md)\.

The default value is `{}`\.

```
extra_json = {}
```

**Note**  
Starting with AWS ParallelCluster version 2\.6\.1, most of the install recipes are skipped by default when launching nodes to improve start up times\. To run all of the install recipes for better backwards compatibility at the expense of startup times, add `"skip_install_recipes" : "no"` to the `cluster` key in the [`extra_json`](#extra-json) setting\. For example:  

```
extra_json = { "cluster" : { "skip_install_recipes" : "no" } }
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `fsx_settings`<a name="fsx-settings"></a>

**\(Optional\)** Specifies the section that defines the Amazon FSx for Lustre configuration\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[fsx]` section](fsx-section.md)\.

For example, the following setting specifies that the section that starts `[fsx fs]` is used for the Amazon FSx for Lustre configuration\.

```
fsx_settings = fs
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `initial_queue_size`<a name="configuration-initial-queue-size"></a>

**\(Optional\)** Sets the initial number of Amazon EC2 instances to launch as compute nodes in the cluster\. If the [`queue_settings`](#queue-settings) setting is defined, then this setting must be removed and replaced by the [`initial_count`](compute-resource-section.md#compute-resource-initial-count) settings in the [`[compute_resource]` sections](compute-resource-section.md)\.

This setting is applicable only for traditional schedulers \(SGE, Slurm, and Torque\)\. If the [`maintain_initial_size`](#maintain-initial-size) setting is `true`, then the [`initial_queue_size`](#configuration-initial-queue-size) setting must be at least one \(1\)\.

If the scheduler is `awsbatch`, use [`min_vcpus`](#min-vcpus) instead\.

Defaults to `2`\.

```
initial_queue_size = 2
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `key_name`<a name="key-name"></a>

Names an existing Amazon EC2 key pair with which to enable SSH access to the instances\.

```
key_name = mykey
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `maintain_initial_size`<a name="maintain-initial-size"></a>

**\(Optional\)** Maintains the initial size of the Auto Scaling group for traditional schedulers \(SGE, Slurm, and Torque\)\.

If the scheduler is `awsbatch`, use [`desired_vcpus`](#desired-vcpus) instead\.

This setting is a Boolean flag\. If set to `true`, the Auto Scaling group doesn't ever have fewer members than the value of [`initial_queue_size`](#configuration-initial-queue-size), and the value of [`initial_queue_size`](#configuration-initial-queue-size) must be one \(1\) or greater\. The cluster can still scale up to the value of [`max_queue_size`](#configuration-max-queue-size)\. If `cluster_type = spot` then the Auto Scaling group can have instances interrupted and the size can drop below [`initial_queue_size`](#configuration-initial-queue-size)\.

If set to `false`, the Auto Scaling group can scale down to zero \(0\) members to prevent resources from sitting idle when they aren't needed\.

If the [`queue_settings`](#queue-settings) setting is defined then this setting must be removed and replaced by the [`initial_count`](compute-resource-section.md#compute-resource-initial-count) and [`min_count`](compute-resource-section.md#compute-resource-min-count) settings in the [`[compute_resource]` sections](compute-resource-section.md)\.

Defaults to `false`\.

```
maintain_initial_size = false
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `master_instance_type`<a name="master-instance-type"></a>

**\(Optional\)** Defines the Amazon EC2 instance type that is used for the head node\. The architecture of the instance type must be the same as the architecture used for the [`compute_instance_type`](#compute-instance-type) setting\.

Defaults to `t2.micro`\.

```
master_instance_type = t2.micro
```

**Note**  
The `p4d.24xlarge` is not supported for the head node\.

**Note**  
Support for AWS Graviton\-based instances \(such as `A1` and `C6g`\) was added in AWS ParallelCluster version 2\.8\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `master_root_volume_size`<a name="master-root-volume-size"></a>

**\(Optional\)** Specifies the head node root volume size in GB\. The AMI must support `growroot`\.

The default value is `25`\.

**Note**  
Before AWS ParallelCluster version 2\.5\.0, the default was 20\.

```
master_root_volume_size = 25
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `max_queue_size`<a name="configuration-max-queue-size"></a>

**\(Optional\)** Sets the maximum number of Amazon EC2 instances that can be launched in the cluster\. If the [`queue_settings`](#queue-settings) setting is defined, then this setting must be removed and replaced by the [`max_count`](compute-resource-section.md#compute-resource-max-count) settings in the [`[compute_resource]` sections](compute-resource-section.md)\.

This setting is applicable only for traditional schedulers \(SGE, Slurm, and Torque\)\.

If the scheduler is `awsbatch`, use [`max_vcpus`](#max-vcpus) instead\.

Defaults to `10`\.

```
max_queue_size = 10
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `max_vcpus`<a name="max-vcpus"></a>

**\(Optional\)** Specifies the maximum number of vCPUs in the compute environment\. Used only if the scheduler is `awsbatch`\.

The default value is `20`\.

```
max_vcpus = 20
```

[Update policy: This setting can't be decreased during an update.](using-pcluster-update.md#update-policy-no-decrease)

## `min_vcpus`<a name="min-vcpus"></a>

**\(Optional\)** Maintains the initial size of the Auto Scaling group for the `awsbatch` scheduler\.

If the scheduler is SGE, Slurm, or Torque, use [`maintain_initial_size`](#maintain-initial-size) instead\.

The compute environment never has fewer members than the value of [`min_vcpus`](#min-vcpus)\.

Defaults to `0`\.

```
min_vcpus = 0
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `placement`<a name="placement"></a>

**\(Optional\)** Defines the cluster placement group logic, enabling either the whole cluster or only the compute instances to use the cluster placement group\.

If the [`queue_settings`](#queue-settings) setting is defined, then this setting should be removed and replaced with [`placement_group`](queue-section.md#queue-placement-group) settings for each of the [`[queue]` sections](queue-section.md)\. If the same placement group is used for different instance types, it’s more likely that the request might fail due to an insufficient capacity error\. For more information, see [Insufficient instance capacity](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html#troubleshooting-launch-capacity) in the *Amazon EC2 User Guide for Linux Instances*\. Multiple queues can only share a placement group if it’s created in advance and configured in the [`placement_group`](queue-section.md#queue-placement-group) setting for each queue\. If each [`[queue]` sections](queue-section.md) defines a [`placement_group`](queue-section.md#queue-placement-group) setting, then the head node can't be in the placement group for a queue\.

Valid options are `cluster` or `compute`\.

This parameter isn't used when the scheduler is `awsbatch`\.

The default value is `compute`\.

```
placement = compute
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `placement_group`<a name="placement-group"></a>

**\(Optional\)** Defines the cluster placement group\. If the [`queue_settings`](#queue-settings) setting is defined, then this setting should be removed and replaced by the [`placement_group`](queue-section.md#queue-placement-group) settings in the [`[queue]` sections](queue-section.md)\.

Valid options are the following values:
+ `NONE`
+ `DYNAMIC`
+ An existing Amazon EC2 cluster placement group name

When set to `DYNAMIC`, a unique placement group is created and deleted as part of the cluster stack\.

This parameter isn't used when the scheduler is `awsbatch`\.

For more information about placement groups, see [Placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The default value is `NONE`\.

Not all instance types support cluster placement groups\. For example, the default instance type of `t2.micro` doesn't support cluster placement groups\. For information about the list of instance types that support cluster placement groups, see [Cluster placement group rules and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-limitations-cluster) in the *Amazon EC2 User Guide for Linux Instances*\. See [Placement groups and instance launch issues](troubleshooting.md#placement-groups-and-instance-launch-issues) for tips when working with placement groups\.

```
placement_group = NONE
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `post_install`<a name="post-install"></a>

**\(Optional\)** Specifies the URL of a post\-install script that is run after all of the `boot_as_*` scripts are run\.

When using `awsbatch` as the scheduler, the post\-install script is run only on the head node\.

The parameter format can be either `http://hostname/path/to/script.sh` or `s3://bucketname/path/to/script.sh`\.

The default value is `NONE`\.

```
post_install = NONE
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `post_install_args`<a name="post-install-args"></a>

**\(Optional\)** Specifies a quoted list of arguments to pass to the post\-install script\.

The default value is `NONE`\.

```
post_install_args = "NONE"
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `pre_install`<a name="pre-install"></a>

**\(Optional\)** Specifies the URL of a pre\-install script that is run before any of the `boot_as_*` scripts are run\.

When using `awsbatch` as the scheduler, the pre\-install script is run only on the head node\.

The parameter format can be either `http://hostname/path/to/script.sh` or `s3://bucketname/path/to/script.sh`\.

The default value is `NONE`\.

```
pre_install = NONE
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `pre_install_args`<a name="pre-install-args"></a>

**\(Optional\)** Specifies a quoted list of arguments to pass to the pre\-install script\.

The default value is `NONE`\.

```
pre_install_args = "NONE"
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `proxy_server`<a name="proxy-server"></a>

**\(Optional\)** Defines an HTTP or HTTPS proxy server, typically `http://x.x.x.x:8080`\.

The default value is `NONE`\.

```
proxy_server = NONE
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `queue_settings`<a name="queue-settings"></a>

**\(Optional\)** Specifies that the cluster uses queues instead of a homogenous compute fleet, and which [`[queue]` sections](queue-section.md) are used\. The first [`[queue]` section](queue-section.md) listed is the default scheduler queue\. The `queue` section names must start with a lowercase letter, contain no more than 30 characters, and only contain lowercase letters, numbers, and hyphens \(\-\)\.

**Important**  
[`queue_settings`](#queue-settings) is only supported when [`scheduler`](#scheduler) is set to `slurm`\. The [`cluster_type`](#cluster-type), [`compute_instance_type`](#compute-instance-type), [`initial_queue_size`](#configuration-initial-queue-size), [`maintain_initial_size`](#maintain-initial-size), [`max_queue_size`](#configuration-max-queue-size), [`placement`](#placement), [`placement_group`](#placement-group), and [`spot_price`](#spot-price) settings must not be specified\. The [`disable_hyperthreading`](#disable-hyperthreading) and [`enable_efa`](#enable-efa) settings can either be specified in the [`[cluster]` section](#cluster-definition) or the [`[queue]` sections](queue-section.md), but not both\.

Up to five \(5\) [`[queue]` sections](queue-section.md) are supported\.

For more information, see the [`[queue]` section](queue-section.md)\.

For example, the following setting specifies that the sections that start `[queue q1]` and `[queue q2]` are used\.

```
queue_settings = q1, q2
```

**Note**  
Support for [`queue_settings`](#queue-settings) was added in AWS ParallelCluster version 2\.9\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `raid_settings`<a name="raid-settings"></a>

**\(Optional\)** Identifies the `[raid]` section with the Amazon EBS volume RAID configuration\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[raid]` section](raid-section.md)\.

For example, the following setting specifies that the section that starts `[raid rs]` be used for the Auto Scaling configuration\.

```
raid_settings = rs
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `s3_read_resource`<a name="s3-read-resource"></a>

**\(Optional\)** Specifies an Amazon S3 resource to which AWS ParallelCluster nodes are granted read\-only access\.

For example, `arn:aws:s3:::my_corporate_bucket/*` provides read\-only access to all objects in the *my\_corporate\_bucket* bucket\.

See [working with Amazon S3](s3_resources.md) for details on format\.

The default value is `NONE`\.

```
s3_read_resource = NONE
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `s3_read_write_resource`<a name="s3-read-write-resource"></a>

**\(Optional\)** Specifies an Amazon S3 resource which AWS ParallelCluster nodes are granted read/write access to\.

For example, `arn:aws:s3:::my_corporate_bucket/Development/*` provides read/write access to all objects in the Development folder of the *my\_corporate\_bucket* bucket\.

See [working with Amazon S3](s3_resources.md) for details on format\.

The default value is `NONE`\.

```
s3_read_write_resource = NONE
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `scaling_settings`<a name="scaling-settings"></a>

Identifies the `[scaling]` section with the Auto Scaling configuration\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[scaling]` section](scaling-section.md)\.

For example, the following setting specifies that the section that starts `[scaling custom]` is used for the Auto Scaling configuration\.

```
scaling_settings = custom
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `scheduler`<a name="scheduler"></a>

**\(Required\)** Defines the cluster scheduler\.

Valid options are the following values:

`awsbatch`  
AWS Batch

`sge`  
Son of Grid Engine \(SGE\)

`slurm`  
Slurm Workload Manager \(Slurm\)

`torque`  
Torque Resource Manager \(Torque\)

**Warning**  
Starting on December 31, 2021, AWS will no longer include SGE and Torque support for all released versions of AWS ParallelCluster\. Previous versions of AWS ParallelCluster that support SGE and Torque will still be available for download and use\. However, these versions will not be eligible for future updates or troubleshooting support from the AWS service and AWS Support teams\. Moreover, future releases of AWS ParallelCluster made before and after December 31, 2021 will not include support for either SGE or Torque\.

For more information about the `awsbatch` scheduler, see [networking setup](networking.md#awsbatch-networking)\.

**Note**  
Before AWS ParallelCluster version 2\.7\.0, the `scheduler` parameter was optional, and the default was `sge`\. Starting with AWS ParallelCluster version 2\.7\.0, the `scheduler` parameter is required\.

```
scheduler = slurm
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `shared_dir`<a name="cluster-shared-dir"></a>

**\(Optional\)** Defines the path where the shared Amazon EBS volume is mounted\.

Do not use this option with multiple Amazon EBS volumes\. Instead, provide [`shared_dir`](#cluster-shared-dir) values under each [`[ebs]` section](ebs-section.md)\.

See the [`[ebs]` section](ebs-section.md) for details on working with multiple Amazon EBS volumes\.

The default value is `/shared`\.

The following example shows a shared Amazon EBS volume mounted at `/myshared`\.

```
shared_dir = myshared
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `spot_bid_percentage`<a name="spot-bid-percentage"></a>

**\(Optional\)** Sets the on\-demand percentage used to calculate the maximum Spot price for the ComputeFleet, when `awsbatch` is the scheduler\.

If unspecified, the current spot market price is selected, capped at the On\-Demand price\.

```
spot_bid_percentage = 85
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `spot_price`<a name="spot-price"></a>

**\(Optional\)** Sets the maximum Spot price for the ComputeFleet on traditional schedulers \(SGE, Slurm, and Torque\)\. Used only when the [`cluster_type`](#cluster-type) setting is set to `spot`\. If you don't specify a value, you are charged the Spot price, capped at the On\-Demand price\. If the [`queue_settings`](#queue-settings) setting is defined, then this setting must be removed and replaced by the [`spot_price`](compute-resource-section.md#compute-resource-spot-price) settings in the [`[compute_resource]` sections](compute-resource-section.md)\.

If the scheduler is `awsbatch`, use [spot\_bid\_percentage](#spot-bid-percentage) instead\.

For assistance finding a Spot Instance that meets your needs, see the [Spot Instance advisor](https://aws.amazon.com/ec2/spot/instance-advisor/)\.

```
spot_price = 1.50
```

**Note**  
In AWS ParallelCluster version 2\.5\.0, if `cluster_type = spot` but [`spot_price`](#spot-price) is not specified, the instance launches of the ComputeFleet fail\. This was fixed in AWS ParallelCluster version 2\.5\.1\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `tags`<a name="tags"></a>

**\(Optional\)** Defines tags to be used by AWS CloudFormation\.

If command line tags are specified via *\-\-tags*, they are merged with config tags\.

Command line tags overwrite config tags that have the same key\.

Tags are JSON formatted\. Don't use quotes outside of the curly braces\.

For more information, see [AWS CloudFormation resource tags type](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-resource-tags.html) in the *AWS CloudFormation User Guide*\.

```
tags = {"key" : "value", "key2" : "value2"}
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

**Note**  
The update policy did not support changing the `tags` setting for AWS ParallelCluster version 2\.8\.0 through version 2\.9\.1\.

## `template_url`<a name="template-url"></a>

**\(Optional\)** Defines the path to the AWS CloudFormation template that is used to create the cluster\.

Updates use the template that was originally used to create the stack\.

Defaults to `https://aws_region_name-aws-parallelcluster.s3.amazonaws.com/templates/aws-parallelcluster-version.cfn.json`\.

```
template_url = https://us-east-1-aws-parallelcluster.s3.amazonaws.com/templates/aws-parallelcluster-2.10.cfn.json
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)

## `vpc_settings`<a name="vpc-settings"></a>

Identifies the `[vpc]` section with the Amazon VPC configuration where the cluster is deployed\. The section name must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

For more information, see the [`[vpc]` section](vpc-section.md)\.

For example, the following setting specifies that the section that starts `[vpc public]` is used for the Amazon VPC configuration\.

```
vpc_settings = public
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)