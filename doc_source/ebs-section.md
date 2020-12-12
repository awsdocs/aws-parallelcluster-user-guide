# `[ebs]` section<a name="ebs-section"></a>

**Topics**
+ [`shared_dir`](#ebs-shared-dir)
+ [`ebs_snapshot_id`](#ebs-snapshot-id)
+ [`volume_type`](#volume-type)
+ [`volume_size`](#volume-size)
+ [`volume_iops`](#volume-iops)
+ [`encrypted`](#encrypted)
+ [`ebs_kms_key_id`](#ebs-kms-key-id)
+ [`ebs_volume_id`](#ebs-volume-id)

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

## `ebs_snapshot_id`<a name="ebs-snapshot-id"></a>

**\(Optional\)** Defines the Amazon EBS snapshot ID if you're using a snapshot as the source for the volume\.

The default value is `NONE`\.

```
ebs_snapshot_id = snap-xxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_type`<a name="volume-type"></a>

**\(Optional\)** Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) of the volume that you want to launch\.

Valid options are the following volume types:
+ `gp2`
+ `io1`
+ `st1`
+ `sc1`

The default value is `gp2`\.

```
volume_type = io1
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_size`<a name="volume-size"></a>

**\(Optional\)** Specifies the size of the volume to be created, in GiB \(if not using a snapshot\)\.

The default value is `20`\.

```
volume_size = 20
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_iops`<a name="volume-iops"></a>

**\(Optional\)** Defines the number of IOPS for `io1`\-type volumes\.

The default value is `100`\.

```
volume_iops = 200
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `encrypted`<a name="encrypted"></a>

**\(Optional\)** Specifies whether the Amazon EBS volume is encrypted\. Note: Do *not* use with snapshots\.

The default value is `false`\.

```
encrypted = false
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

## `ebs_volume_id`<a name="ebs-volume-id"></a>

**\(Optional\)** Defines the volume ID of an existing Amazon EBS volume to attach to the head node\.

The default value is `NONE`\.

```
ebs_volume_id = vol-xxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)