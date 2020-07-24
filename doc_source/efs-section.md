# `[efs]` Section<a name="efs-section"></a>

**Topics**
+ [`efs_fs_id`](#efs-efs-fs-id)
+ [`efs_kms_key_id`](#efs-efs-kms-key-id)
+ [`encrypted`](#efs-encrypted)
+ [`performance_mode`](#efs-performance-mode)
+ [`provisioned_throughput`](#efs-provisioned-throughput)
+ [`shared_dir`](#efs-shared-dir)
+ [`throughput_mode`](#efs-throughput-mode)

Defines configuration settings for the Amazon EFS that is mounted on the master and compute instances\. For more information, see [CreateFileSystem](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html) in the Amazon EFS documentation\.

The format is `[efs <efsname>]`\.

```
[efs customfs]
shared_dir = efs
encrypted = false
performance_mode = generalPurpose
```

## `efs_fs_id`<a name="efs-efs-fs-id"></a>

Defines the Amazon EFS file system ID for an existing file system\.

Specifying this option voids all other Amazon EFS options except for `shared_dir`\.

If you set this option to `config_sanity`, it only supports file systems:
+ That do not have a mount target in the stack's Availability Zone

  OR
+ That do have an existing mount target in the stack's Availability Zone, with inbound and outbound NFS traffic allowed from `0.0.0.0/0`\.

The sanity check for validating `efs_fs_id` requires the IAM role to have the following permissions:
+ `elasticfilesystem:DescribeMountTargets`
+ `elasticfilesystem:DescribeMountTargetSecurityGroups`
+ `ec2:DescribeSubnets`
+ `ec2:DescribeSecurityGroups`
+ `ec2:DescribeNetworkInterfaceAttribute`

To avoid errors, you must add these permissions to your IAM role, or set `sanity_check = false`\.

CAUTION: When you set a mount target with inbound and outbound NFS traffic allowed from `0.0.0.0/0`, it exposes the file system to NFS mounting requests from anywhere in the mount target's Availability Zone\. AWS recommends that you *not* create a mount target in the stack's Availability Zone, and instead let AWS handle this step\. If you must have a mount target in the stack's Availability Zone, consider using a custom security group by providing a `vpc_security_group_id` option under the [[vpc] section](vpc-section.md)\. Then add that security group to the mount target, and turn off config sanity to create the cluster\.

The default value is `NONE`\.

```
efs_fs_id = fs-12345
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `efs_kms_key_id`<a name="efs-efs-kms-key-id"></a>

\(Optional\) Identifies the AWS Key Management Service \(AWS KMS\) customer managed key \(CMK\) to be used to protect the encrypted file system\. If this is set, the `` setting must be set to `true`\. This corresponds to the [KmsKeyId](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html#efs-CreateFileSystem-request-KmsKeyId) parameter in the *Amazon EFS API Reference*\.

The default value is `NONE`\.

```
efs_kms_key_id = 1234abcd-12ab-34cd-56ef-1234567890ab
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `encrypted`<a name="efs-encrypted"></a>

Indicates whether the file system is encrypted\. This corresponds to the [Encrypted](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html#efs-CreateFileSystem-request-Encrypted) parameter in the *Amazon EFS API Reference*\.

The default value is `false`\.

```
encrypted = true
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `performance_mode`<a name="efs-performance-mode"></a>

Defines the performance mode of the file system\. This corresponds to the [PerformanceMode](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html#efs-CreateFileSystem-request-PerformanceMode) parameter in the *Amazon EFS API Reference*\.

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

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `provisioned_throughput`<a name="efs-provisioned-throughput"></a>

Defines the provisioned throughput of the file system, measured in MiB/s\. This corresponds to the [ProvisionedThroughputInMibps](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html#efs-CreateFileSystem-response-ProvisionedThroughputInMibps) parameter in the *Amazon EFS API Reference*\.

If you use this parameter, you must set `` to `provisioned`\.

The limit on throughput is `1024` MiB/s\. To request a limit increase, contact AWS Support\.

The minimum value is `0.0` MiB/s\.

```
provisioned_throughput = 1024
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `shared_dir`<a name="efs-shared-dir"></a>

Defines the Amazon EFS mount point on the master and compute nodes\.

This parameter is required\. The Amazon EFS section is used only if `shared_dir` is specified\.

Do not use `NONE` or `/NONE` as the shared directory\.

The following example mounts Amazon EFS at `/efs`\.

```
shared_dir = efs
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `throughput_mode`<a name="efs-throughput-mode"></a>

Defines the throughput mode of the file system\. This corresponds to the [ThroughputMode](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html#efs-CreateFileSystem-request-ThroughputMode) parameter in the *Amazon EFS API Reference*\.

Valid options are:
+ `bursting`
+ `provisioned`

```
throughput_mode = provisioned
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)