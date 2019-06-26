# `[efs]` Section<a name="efs-section"></a>

**Topics**
+ [`shared_dir`](#id6)
+ [`encrypted`](#id7)
+ [`performance_mode`](#performance-mode)
+ [`throughput_mode`](#throughput-mode)
+ [`provisioned_throughput`](#provisioned-throughput)
+ [`efs_fs_id`](#efs-fs-id)

Defines configuration settings for the Amazon EFS that is mounted on the master and compute instances\. For more information, see [CreateFileSystem](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html) in the Amazon EFS documentation\.

```
[efs customfs]
shared_dir = efs
encrypted = false
performance_mode = generalPurpose
```

## `shared_dir`<a name="id6"></a>

Defines the Amazon EFS mount point on the master and compute nodes\.

This parameter is required\. The Amazon EFS section is used only if `shared_dir` is specified\.

Do not use `NONE` or `/NONE` as the shared directory\.

The following example mounts Amazon EFS at `/efs`\.

```
shared_dir = efs
```

## `encrypted`<a name="id7"></a>

Indicates whether the file system is encrypted\.

The default value is `false`\.

```
encrypted = false
```

## `performance_mode`<a name="performance-mode"></a>

Defines the performance mode of the file system\.

Valid choices are:
+ `generalPurpose`
+ `maxIO`

 Both values are case\-sensitive\.

We recommend the `generalPurpose` performance mode for most file systems\.

File systems that use the `maxIO` performance mode can scale to higher levels of aggregate throughput and operations per second\. However, there is a trade\-off of slightly higher latencies for most file operations\.

This parameter cannot be changed after the file system has been created\.

The default value is `generalPurpose`\.

```
performance_mode = generalPurpose
```

## `throughput_mode`<a name="throughput-mode"></a>

Defines the throughput mode of the file system\.

Valid options are:
+ `bursting`
+ `provisioned`

```
throughput_mode = provisioned
```

## `provisioned_throughput`<a name="provisioned-throughput"></a>

Defines the provisioned throughput of the file system, measured in MiB/s\.

If you use this parameter, you must set `[`throughput_mode`](#throughput-mode)` to `provisioned`\.

The limit on throughput is `1024` MiB/s\. To request a limit increase, contact AWS Support\.

The minimum value is `0.0` MiB/s

```
provisioned_throughput = 1024
```

## `efs_fs_id`<a name="efs-fs-id"></a>

Defines the Amazon EFS file system ID for an existing file system\.

Specifying this option voids all other Amazon EFS options except for `shared_dir`\.

If you set this option to `config_sanity`, it only supports file systems:
+ That do not have a mount target in the stack's Availability Zone

  OR
+ That do have an existing mount target in the stack's Availability Zone, with inbound and outbound NFS traffic allowed from `0.0.0.0/0`

The sanity check for validating `efs_fs_id` requires the IAM role to have the following permissions:
+ `efs:DescribeMountTargets`
+ `efs:DescribeMountTargetSecurityGroups`
+ `ec2:DescribeSubnets`
+ `ec2:DescribeSecurityGroups`
+ `ec2:DescribeNetworkInterfaceAttribute`

To avoid errors, you must add these permissions to your IAM role, or set `sanity_check = false`\.

CAUTION: When you set a mount target with inbound and outbound NFS traffic allowed from `0.0.0.0/0`, it exposes the file system to NFS mounting requests from anywhere in the mount target's Availability Zone\. AWS recommends that you *not* create a mount target in the stack's Availability Zone, and instead let AWS handle this step\. If you must have a mount target in the stack's Availability Zone, consider using a custom security group by providing a `vpc_security_group_id` option under the [[vpc] section](vpc-section.md)\. Then add that security group to the mount target, and turn off config sanity to create the cluster\.

The default value is `NONE`\.

```
efs_fs_id = fs-12345
```
