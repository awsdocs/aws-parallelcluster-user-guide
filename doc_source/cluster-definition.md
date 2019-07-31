# `[cluster]` Section<a name="cluster-definition"></a>

**Topics**
+ [`additional_cfn_template`](#additional-cfn-template)
+ [`base_os`](#base-os)
+ [`cluster_type`](#cluster-type)
+ [`compute_instance_type`](#compute-instance-type)
+ [`compute_root_volume_size`](#compute-root-volume-size)
+ [`custom_ami`](#custom-ami-section)
+ [`desired_vcpus`](#desired-vcpus)
+ [`ebs_settings`](#ebs-settings)
+ [`ec2_iam_role`](#ec2-iam-role)
+ [`efs_settings`](#efs-settings)
+ [`enable_efa`](#enable-efa)
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

Defines one or more clusters for different job types or workloads\.

Each cluster can have its own configuration\.

The format is `[cluster <clustername>]`\. The `[[cluster] section](#cluster-definition)` named by the `[`cluster_template`](global.md#cluster-template)` setting in the `[[global] section](global.md)` is used\.

```
[cluster default]
```

## `additional_cfn_template`<a name="additional-cfn-template"></a>

Defines an additional AWS CloudFormation template to launch along with the cluster\. This additional template is used for the creation of resources that exist outside of the cluster but are part of the cluster's lifecycle\.

When set to a value other than `NONE`, it must be an HTTP URL to a public template, with all parameters provided\.

The default value is `NONE`\.

```
additional_cfn_template = NONE
```

## `base_os`<a name="base-os"></a>

Specifies which OS type is used in the cluster\.

Available options are:
+ `alinux`
+ `centos6`
+ `centos7`
+ `ubuntu1404`
+ `ubuntu1604`

Supported operating systems by Region are listed in the following table\. Note that "commercial" entails all other supported Regions including us\-east\-1, us\-west\-2, and so on\.


| Partition \(Regions\) | `alinux` | `centos6` | `centos7` | `ubuntu1404` | `ubuntu1604` | 
| --- | --- | --- | --- | --- | --- | 
| Commercial \(All Regions not mentioned below\) | True | True | True | True | True | 
| GovCloud \(us\-gov\-east\-1\) | True | False | False | True | True | 
| GovCloud \(us\-gov\-west\-1\) | True | False | False | True | True | 
| China \(cn\-north\-1\) | True | False | False | True | True | 
| China \(cn\-northwest\-1\) | True | False | False | False | False | 

Note: The `base_os` parameter also determines the user name that is used to log into the cluster\.
+ CentOS 6 and 7: `centos` 
+ Ubuntu 14\.04 LTS and 16\.04 LTS: `ubuntu` 
+ Amazon Linux: `ec2-user` 

The default value is `alinux`\.

```
base_os = alinux
```

## `cluster_type`<a name="cluster-type"></a>

Defines the type of cluster to launch\.

Valid options are: `ondemand`, and `spot`\.

The default value is `ondemand`\.

```
cluster_type = ondemand
```

## `compute_instance_type`<a name="compute-instance-type"></a>

Defines the Amazon EC2 instance type that is used for the cluster compute nodes\.

If you are using the `awsbatch` scheduler, see the Compute Environments creation in the AWS Batch UI for a list of supported instance types\.

Defaults to `t2.micro`, `optimal` when the scheduler is `awsbatch`\.

```
compute_instance_type = t2.micro
```

## `compute_root_volume_size`<a name="compute-root-volume-size"></a>

Specifies the ComputeFleet root volume size in GB\. The AMI must support growroot\.

The default value is `20`\.

```
compute_root_volume_size = 20
```

## `custom_ami`<a name="custom-ami-section"></a>

Specifies the ID of a custom AMI to use instead of the default [published AMIs](https://github.com/aws/aws-parallelcluster/blob/master/amis.txt)\.

The default value is `NONE`\.

```
custom_ami = NONE
```

## `desired_vcpus`<a name="desired-vcpus"></a>

Specifies the desired number of vCPUs in the compute environment\. Used only if the scheduler is `awsbatch`\.

The default value is `4`\.

```
desired_vcpus = 4
```

## `ebs_settings`<a name="ebs-settings"></a>

Identifies the `[ebs]` sections with the Amazon EBS volumes that are mounted on the master instance\. When using multiple Amazon EBS volumes, enter these parameters as a comma\-separated list\.

Up to five \(5\) additional Amazon EBS volumes are supported\.

For more information, see the [[ebs] section](ebs-section.md)\.

For example, the following setting specifies that the sections that start `[ebs custom1]` and `[ebs custom2]` are used for the Amazon EBS volumes\.

```
ebs_settings = custom1, custom2
```

## `ec2_iam_role`<a name="ec2-iam-role"></a>

Defines the name of an existing IAM role for Amazon EC2 that is attached to all instances in the cluster\. An IAM role name and its Amazon Resource Name \(ARN\) are different\. ARNs cannot be used as an argument to `ec2_iam_role`\.

The default value is `NONE`\.

```
ec2_iam_role = NONE
```

## `efs_settings`<a name="efs-settings"></a>

Specifies settings related to the Amazon EFS filesystem\.

For more information, see the [[efs] section](efs-section.md)\.

For example, the following setting specifies that the section that starts `[efs customfs]` is used for the Amazon EFS filesystem configuration\.

```
efs_settings = customfs
```

## `enable_efa`<a name="enable-efa"></a>

If present, specifies that Elastic Fabric Adapter \(EFA\) is enabled for the compute nodes\. For more information, see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
enable_efa = compute
```

## `encrypted_ephemeral`<a name="encrypted-ephemeral"></a>

Encrypts the ephemeral instance store volumes with non\-recoverable in\-memory keys, using LUKS \(Linux Unified Key Setup\)\.

For more information, see [https://gitlab.com/cryptsetup/cryptsetup/blob/master/README.md](https://gitlab.com/cryptsetup/cryptsetup/blob/master/README.md)\.

The default value is `false`\.

```
encrypted_ephemeral = false
```

## `ephemeral_dir`<a name="ephemeral-dir"></a>

Defines the path where instance store volumes are mounted, if they are used\.

The default value is `/scratch`\.

```
ephemeral_dir = /scratch
```

## `extra_json`<a name="extra-json"></a>

Defines the extra JSON that is merged into the `dna.json` that is used by Chef\.

The default value is `{}`\.

```
extra_json = {}
```

## `fsx_settings`<a name="fsx-settings"></a>

Specifies the section that defines the Amazon FSx for Lustre configuration\.

For more information, see the [[fsx] section](fsx-section.md)\.

```
fsx_settings = fs
```

For example, the following setting specifies that the section that starts `[fsx fs]` is used for the Amazon EFS filesystem configuration\.

```
fsx_settings = fs
```

## `initial_queue_size`<a name="configuration-initial-queue-size"></a>

Sets the initial number of Amazon EC2 instances to launch as compute nodes in the cluster\.

This setting is applicable only for traditional schedulers \(SGE, Slurm, and Torque\)\.

If the scheduler is `awsbatch`, use [`min_vcpus`](#min-vcpus) instead\.

Defaults to `2`\.

```
initial_queue_size = 2
```

## `key_name`<a name="key-name"></a>

Names an existing Amazon EC2 key pair with which to enable SSH access to the instances\.

```
key_name = mykey
```

## `maintain_initial_size`<a name="maintain-initial-size"></a>

Maintains the initial size of the Auto Scaling group for traditional schedulers \(SGE, Slurm, and Torque\)\.

If the scheduler is `awsbatch`, use `[`desired_vcpus`](#desired-vcpus)` instead\.

This setting is a Boolean flag\. If set to `true`, the Auto Scaling group never has fewer members than the value of `[`initial_queue_size`](#configuration-initial-queue-size)`\. The cluster can still scale up to the value of `[`max_queue_size`](#configuration-max-queue-size)`\.

If set to `false`, the Auto Scaling group can scale down to zero \(0\) members to prevent resources from sitting idle when they are not needed\.

Defaults to `false`\.

```
maintain_initial_size = false
```

## `master_instance_type`<a name="master-instance-type"></a>

Defines the Amazon EC2 instance type that is used for the master node\.

Defaults to `t2.micro`\.

```
master_instance_type = t2.micro
```

## `master_root_volume_size`<a name="master-root-volume-size"></a>

Specifies the MasterServer root volume size in GB\. The AMI must support growroot\.

The default value is `20`\.

```
master_root_volume_size = 20
```

## `max_queue_size`<a name="configuration-max-queue-size"></a>

Sets the maximum number of Amazon EC2 instances that can be launched in the cluster\.

This setting is applicable only for traditional schedulers \(SGE, Slurm, and Torque\)\.

If the scheduler is `awsbatch`, use [`max_vcpus`](#max-vcpus) instead\.

Defaults to `10`\.

```
max_queue_size = 10
```

## `max_vcpus`<a name="max-vcpus"></a>

Specifies the maximum number of vCPUs in the compute environment\. Used only if the scheduler is `awsbatch`\.

The default value is `20`\.

```
max_vcpus = 20
```

## `min_vcpus`<a name="min-vcpus"></a>

Maintains the initial size of the Auto Scaling group for the `awsbatch` scheduler\.

If the scheduler is SGE, Slurm, or Torque, use `[`maintain_initial_size`](#maintain-initial-size)` instead\.

The compute environment never has fewer members than the value of `min_vcpus`\.

Defaults to `0`\.

```
min_vcpus = 0
```

## `placement`<a name="placement"></a>

Defines the cluster placement group logic, enabling either the whole cluster or only the compute instances to use the cluster placement group\.

Valid options are `cluster` or `compute`\.

This parameter is not used when the scheduler is `awsbatch`\.

The default value is `compute`\.

```
placement = compute
```

## `placement_group`<a name="placement-group"></a>

Defines the cluster placement group\.

Valid options are:
+ `NONE`
+ `DYNAMIC`
+ An existing Amazon EC2 cluster placement group name

When set to `DYNAMIC`, a unique placement group is created and deleted as part of the cluster stack\.

This parameter is not used when the scheduler is `awsbatch`\.

For more information about placement groups, see [Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The default value is `NONE`\.

```
placement_group = NONE
```

## `post_install`<a name="post-install"></a>

Specifies the URL of a postinstall script that is executed after all of the `boot_as_*` scripts are run\.

When using `awsbatch` as the scheduler, the postinstall script is executed only on the master node\.

The parameter format can be either `http://hostname/path/to/script.sh` or `s3://bucketname/path/to/script.sh`\.

The default value is `NONE`\.

```
post_install = NONE
```

## `post_install_args`<a name="post-install-args"></a>

Specifies a list of arguments to pass to the postinstall script\.

The default value is `NONE`\.

```
post_install_args = NONE
```

## `pre_install`<a name="pre-install"></a>

Specifies the URL of a preinstall script that is executed before any of the `boot_as_*` scripts are run\.

When using `awsbatch` as the scheduler, the preinstall script is executed only on the master node\.

The parameter format can be either `http://hostname/path/to/script.sh` or `s3://bucketname/path/to/script.sh`\.

The default value is `NONE`\.

```
pre_install = NONE
```

## `pre_install_args`<a name="pre-install-args"></a>

Specifies a quoted list of arguments to pass to the preinstall script\.

The default value is `NONE`\.

```
pre_install_args = NONE
```

## `proxy_server`<a name="proxy-server"></a>

Defines an HTTP or HTTPS proxy server, typically `http://x.x.x.x:8080`\.

The default value is `NONE`\.

```
proxy_server = NONE
```

## `raid_settings`<a name="raid-settings"></a>

Identifies the `[raid]` section with the Amazon EBS volume RAID configuration\.

For more information, see the [[raid] section](raid-section.md)\.

For example, the following setting specifies that the section that starts `[raid rs]` be used for the Auto Scaling configuration\.

```
raid_settings = rs
```

## `s3_read_resource`<a name="s3-read-resource"></a>

Specifies an Amazon S3 resource to which AWS ParallelCluster nodes are granted read\-only access\.

For example, `arn:aws:s3:::my_corporate_bucket/*` provides read\-only access to all objects in the *my\_corporate\_bucket* bucket\.

See [working with Amazon S3](s3_resources.md) for details on format\.

The default value is `NONE`\.

```
s3_read_resource = NONE
```

## `s3_read_write_resource`<a name="s3-read-write-resource"></a>

Specifies an Amazon S3 resource to which AWS ParallelCluster nodes are granted read/write access\.

For example, `arn:aws:s3:::my_corporate_bucket/Development/*` provides read/write access to all objects in the Development folder of the *my\_corporate\_bucket* bucket\.

See [working with Amazon S3](s3_resources.md) for details on format\.

The default value is `NONE`\.

```
s3_read_write_resource = NONE
```

## `scaling_settings`<a name="scaling-settings"></a>

Identifies the `[scaling]` section with the Auto Scaling configuration\.

For more information, see the [[scaling] section](scaling-section.md)\.

For example, the following setting specifies that the section that starts `[scaling custom]` is used for the Auto Scaling configuration\.

```
scaling_settings = custom
```

## `scheduler`<a name="scheduler"></a>

Defines the cluster scheduler\.

Valid options are:
+ `sge`
+ `torque`
+ `slurm`
+ `awsbatch`

For more information about the `awsbatch` scheduler, see [networking setup](networking.md#awsbatch-networking)\.

The default value is `sge`\.

```
scheduler = sge
```

## `shared_dir`<a name="cluster-shared-dir"></a>

Defines the path where the shared Amazon EBS volume is mounted\.

Do not use this option with multiple Amazon EBS volumes\. Instead, provide `shared_dir` values under each Amazon EBS [[ebs] section](ebs-section.md)\.

See the [Amazon EBS Section](ebs-section.md) for details on working with multiple Amazon EBS volumes\.

The default value is `/shared`\.

The following example shows a shared Amazon EBS volume mounted at `/myshared`\.

```
shared_dir = myshared
```

## `spot_bid_percentage`<a name="spot-bid-percentage"></a>

Optionally sets the on\-demand bid percentage used to calculate the maximum Spot price for the ComputeFleet, when `awsbatch` is the scheduler\.

If unspecified, the current spot market price is selected, capped at the On\-Demand price\.

```
spot_bid_percentage = 85
```

## `spot_price`<a name="spot-price"></a>

Optionally sets the maximum Spot price for the ComputeFleet on traditional schedulers \(SGE, Slurm, and Torque\)\. Used only when the `cluster_type` is set to `spot`\. If you do not specify a value, you are charged the Spot price, capped at the On\-Demand price\.

If the scheduler is `awsbatch`, use [spot\_bid\_percentage](#spot-bid-percentage) instead\.

For assistance finding a bid price that meets your needs, see the [Spot Bid Advisor](https://aws.amazon.com/ec2/spot/bid-advisor/)\.

```
spot_price = 1.50
```

## `tags`<a name="tags"></a>

Defines tags to be used by AWS CloudFormation\.

If command line tags are specified via *\-\-tags*, they are merged with config tags\.

Command line tags overwrite config tags that have the same key\.

Tags are JSON formatted\. Do not use quotes outside of the curly braces\.

For more information, see [AWS CloudFormation Resource Tags Type](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-resource-tags.html) in the *AWS CloudFormation User Guide*\.

```
tags = {"key" : "value", "key2" : "value2"}
```

## `template_url`<a name="template-url"></a>

Defines the path to the AWS CloudFormation template that is used to create the cluster\.

Updates use the template that was originally used to create the stack\.

Defaults to `https://<aws_region_name>-aws-parallelcluster.s3.amazonaws.com/templates/aws-parallelcluster-<version>.cfn.json`\.

```
template_url = https://us-east-1-aws-parallelcluster.s3.amazonaws.com/templates/aws-parallelcluster.cfn.json
```

## `vpc_settings`<a name="vpc-settings"></a>

Identifies the `[vpc]` section with the Amazon VPC configuration where the cluster is deployed\.

For more information, see the [[vpc] section](vpc-section.md)\.

For example, the following setting specifies that the section that starts `[vpc public]` is used for the Amazon VPC configuration\.

```
vpc_settings = public
```