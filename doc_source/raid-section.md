# `[raid]` section<a name="raid-section"></a>

**Topics**
+ [`shared_dir`](#raid-shared-dir)
+ [`ebs_kms_key_id`](#raid-ebs_kms_key_id)
+ [`encrypted`](#raid-encrypted)
+ [`num_of_raid_volumes`](#num-of-raid-volumes)
+ [`raid_type`](#raid-type)
+ [`volume_iops`](#raid-volume-iops)
+ [`volume_size`](#raid-volume-size)
+ [`volume_throughput`](#raid-volume-throughput)
+ [`volume_type`](#raid-volume-type)

Defines configuration settings for a RAID array that's built from a number of identical Amazon EBS volumes\. The RAID drive is mounted on the head node and is exported to compute nodes with NFS\.

The format is `[raid raid-name]`\. *raid\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[raid rs]
shared_dir = raid
raid_type = 1
num_of_raid_volumes = 2
encrypted = true
```

## `shared_dir`<a name="raid-shared-dir"></a>

**\(Required\)** Defines the mount point for the RAID array on the head and compute nodes\.

The RAID drive is created only if this parameter is specified\.

Don't use `NONE` or `/NONE` as the shared directory\.

The following example mounts the array at `/raid`\.

```
shared_dir = raid
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_kms_key_id`<a name="raid-ebs_kms_key_id"></a>

**\(Optional\)** Specifies a custom AWS KMS key to use for encryption\.

This parameter must be used together with `encrypted = true`, and it must have a custom [`ec2_iam_role`](cluster-definition.md#ec2-iam-role)\.

For more information, see [Disk encryption with a custom KMS Key](tutorials_04_encrypted_kms_fs.md)\.

```
ebs_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `encrypted`<a name="raid-encrypted"></a>

**\(Optional\)** Specifies whether the file system is encrypted\.

The default value is `false`\.

```
encrypted = false
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `num_of_raid_volumes`<a name="num-of-raid-volumes"></a>

**\(Optional\)** Defines the number of Amazon EBS volumes to assemble the RAID array from\.

Minimum number of volumes is `2`\.

Maximum number of volumes is `5`\.

The default value is `2`\.

```
num_of_raid_volumes = 2
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `raid_type`<a name="raid-type"></a>

**\(Required\)** Defines the RAID type for the RAID array\.

The RAID drive is created only if this parameter is specified\.

Valid options are the following values:
+ `0`
+ `1`

For more information on RAID types, see [RAID info](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The following example creates a RAID `0` array:

```
raid_type = 0
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_iops`<a name="raid-volume-iops"></a>

**\(Optional\)** Defines the number of IOPS for `io1`, `io2`, and `gp3` type volumes\.

The default value, supported values, and `volume_iops` to `volume_size` ratio varies by [`volume_type`](#raid-volume-type) and [`volume_size`](#raid-volume-size)\.

`volume_type` = `io1`  
Default `volume_iops` = 100  
Supported values `volume_iops` = 100–64000 †  
Maximum `volume_iops` to `volume_size` ratio = 50 IOPS per GiB\. 5000 IOPS requires a `volume_size` of at least 100 GiB\.

`volume_type` = `io2`  
Default `volume_iops` = 100  
Supported values `volume_iops` = 100–64000 \(256000 for `io2` Block Express volumes\) †  
Maximum `volume_iops` to `volume_size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `volume_size` of at least 10 GiB\.

`volume_type` = `gp3`  
Default `volume_iops` = 3000  
Supported values `volume_iops` = 3000–16000  
Maximum `volume_iops` to `volume_size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `volume_size` of at least 10 GiB\.

```
volume_iops = 3000
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

† Maximum IOPS is guaranteed only on [Instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS\. Older `io1` volumes might not reach full performance unless you [modify the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\. `io2` Block Express volumes support `volume_iops` values up to 256000\. For more information, see [`io2` Block Express volumes \(In preview\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) in the *Amazon EC2 User Guide for Linux Instances*\.

## `volume_size`<a name="raid-volume-size"></a>

**\(Optional\)** Defines the size of the volume to be created, in GiB\.

The default value and supported values varies by [`volume_type`](#raid-volume-type)\.

`volume_type` = `standard`  
Default `volume_size` = 20 GiB  
Supported values `volume_size` = 1–1024 GiB

`volume_type` = `gp2`, `io1`, `io2`, and `gp3`  
Default `volume_size` = 20 GiB  
Supported values `volume_size` = 1–16384 GiB

`volume_type` = `sc1` and `st1`  
Default `volume_size` = 500 GiB  
Supported values `volume_size` = 500–16384 GiB

```
volume_size = 20
```

**Note**  
Before AWS ParallelCluster version 2\.10\.1, the default value for all volume types was 20 GiB\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_throughput`<a name="raid-volume-throughput"></a>

**\(Optional\)** Defines the throughput for `gp3` volume types, in MiB/s\.

The default value is `125`\.

Supported values `volume_throughput` = 125–1000 MiB/s

The ratio of `volume_throughput` to `volume_iops` can be no more than 0\.25\. The maximum throughput of 1000 MiB/s requires that the `volume_iops` setting is at least 4000\.

```
volume_throughput = 1000
```

**Note**  
Support for `volume_throughput` was added in AWS ParallelCluster version 2\.10\.1\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_type`<a name="raid-volume-type"></a>

**\(Optional\)** Defines the type of volume to build\.

Valid options are the following values:

`gp2`, `gp3`  
General purpose SSD

`io1`, `io2`  
Provisioned IOPS SSD

`st1`  
Throughput optimized HDD

`sc1`  
Cold HDD

`standard`  
Previous generation magnetic

For more information, see [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The default value is `gp2`\.

```
volume_type = io2
```

**Note**  
Support for `gp3` and `io2` was added in AWS ParallelCluster version 2\.10\.1\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)