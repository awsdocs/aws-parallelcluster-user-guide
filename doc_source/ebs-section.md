# `[ebs]` section<a name="ebs-section"></a>

**Topics**
+ [`shared_dir`](#ebs-shared-dir)
+ [`ebs_kms_key_id`](#ebs-kms-key-id)
+ [`ebs_snapshot_id`](#ebs-snapshot-id)
+ [`ebs_volume_id`](#ebs-volume-id)
+ [`encrypted`](#encrypted)
+ [`volume_iops`](#volume-iops)
+ [`volume_size`](#volume-size)
+ [`volume_throughput`](#volume-throughput)
+ [`volume_type`](#volume-type)

Defines Amazon EBS volume configuration settings for volumes that are mounted on the head node and shared via NFS to the compute nodes\.

The format is `[ebs ebs-name]`\. *ebs\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[ebs custom1]
shared_dir = vol1
ebs_snapshot_id = snap-xxxxx
volume_type = io1
volume_iops = 200
...

[ebs custom2]
shared_dir = vol2
...

...
```

## `shared_dir`<a name="ebs-shared-dir"></a>

**\(Required\)** Specifies the path where the shared Amazon EBS volume is mounted\.

This parameter is required when using multiple Amazon EBS volumes\.

When using one \(1\) Amazon EBS volume, this option overwrites the [`shared_dir`](cluster-definition.md#cluster-shared-dir) that's specified under the [`[cluster]` section](cluster-definition.md)\. In the following example, the volume mounts to `/vol1`\.

```
shared_dir = vol1
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_kms_key_id`<a name="ebs-kms-key-id"></a>

**\(Optional\)** Specifies a custom AWS KMS key to use for encryption\.

This parameter must be used together with `encrypted = true`\. It also must have a custom [`ec2_iam_role`](cluster-definition.md#ec2-iam-role)\.

For more information, see [Disk encryption with a custom KMS Key](tutorials_04_encrypted_kms_fs.md)\.

```
ebs_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_snapshot_id`<a name="ebs-snapshot-id"></a>

**\(Optional\)** Defines the Amazon EBS snapshot ID if you're using a snapshot as the source for the volume\.

There is no default value\.

```
ebs_snapshot_id = snap-xxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_volume_id`<a name="ebs-volume-id"></a>

**\(Optional\)** Defines the volume ID of an existing Amazon EBS volume to attach to the head node\.

There is no default value\.

```
ebs_volume_id = vol-xxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `encrypted`<a name="encrypted"></a>

**\(Optional\)** Specifies whether the Amazon EBS volume is encrypted\. Note: Do *not* use with snapshots\.

The default value is `false`\.

```
encrypted = false
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_iops`<a name="volume-iops"></a>

**\(Optional\)** Defines the number of IOPS for `io1`, `io2`, and `gp3` type volumes\.

The default value, supported values, and `volume_iops` to `volume_size` ratio varies by [`volume_type`](raid-section.md#raid-volume-type) and [`volume_size`](#volume-size)\.

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
volume_iops = 200
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

† Maximum IOPS is guaranteed only on [Instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS\. Older `io1` volumes might not reach full performance unless you [modify the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\. `io2` Block Express volumes support `volume_iops` values up to 256000\. For more information, see [`io2` Block Express volumes \(In preview\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) in the *Amazon EC2 User Guide for Linux Instances*\.

## `volume_size`<a name="volume-size"></a>

**\(Optional\)** Specifies the size of the volume to be created, in GiB \(if not using a snapshot\)\.

The default value and supported values varies by [`volume_type`](#volume-type)\.

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

## `volume_throughput`<a name="volume-throughput"></a>

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

## `volume_type`<a name="volume-type"></a>

**\(Optional\)** Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) of the volume that you want to launch\.

Valid options are the following volume types:

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