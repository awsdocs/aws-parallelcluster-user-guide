# `[ebs]` Section<a name="ebs-section"></a>

**Topics**
+ [`shared_dir`](#ebs-shared-dir)
+ [`ebs_snapshot_id`](#ebs-snapshot-id)
+ [`volume_type`](#volume-type)
+ [`volume_size`](#volume-size)
+ [`volume_iops`](#volume-iops)
+ [`encrypted`](#encrypted)
+ [`ebs_kms_key_id`](#ebs-kms-key-id)
+ [`ebs_volume_id`](#ebs-volume-id)

Defines Amazon EBS volume configuration settings for volumes that are mounted on the master instance and shared via NFS to the compute nodes\.

The format is `[ebs <ebsname>]`\.

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

Specifies the path where the shared Amazon EBS volume is mounted\.

This parameter is required when using multiple Amazon EBS volumes\.

When using one \(1\) Amazon EBS volume, this option overwrites the [`shared_dir`](cluster-definition.md#cluster-shared-dir) that is specified under the [[cluster] section](cluster-definition.md)\. In the following example, the volume mounts to `/vol1`\.

```
shared_dir = vol1
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_snapshot_id`<a name="ebs-snapshot-id"></a>

Defines the Amazon EBS snapshot Id, if you are using a snapshot as the source for the volume\.

The default value is `NONE`\.

```
ebs_snapshot_id = snap-xxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_type`<a name="volume-type"></a>

Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) of the volume that you want to launch\.

Valid options are:
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

Specifies the size of the volume to be created, in GiB \(if not using a snapshot\)\.

The default value is `20`\.

```
volume_size = 20
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `volume_iops`<a name="volume-iops"></a>

Defines the number of IOPS for `io1`\-type volumes\.

```
volume_iops = 200
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `encrypted`<a name="encrypted"></a>

Specifies whether the Amazon EBS volume is encrypted\. Note: Do *not* use with snapshots\.

The default value is `false`\.

```
encrypted = false
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_kms_key_id`<a name="ebs-kms-key-id"></a>

Specifies a custom AWS KMS key to use for encryption\.

This parameter must be used together with `encrypted = true`\. It also must have a custom `ec2_iam_role`\.

For more information, see [Disk Encryption with a Custom KMS Key](tutorials_04_encrypted_kms_fs.md)\.

```
ebs_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ebs_volume_id`<a name="ebs-volume-id"></a>

Defines the volume Id of an existing Amazon EBS volume to attach to the master instance\.

The default value is `NONE`\.

```
ebs_volume_id = vol-xxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)