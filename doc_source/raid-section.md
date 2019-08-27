# `[raid]` Section<a name="raid-section"></a>

**Topics**
+ [`shared_dir`](#id8)
+ [`raid_type`](#raid-type)
+ [`num_of_raid_volumes`](#num-of-raid-volumes)
+ [`volume_type`](#id9)
+ [`volume_size`](#id11)
+ [`volume_iops`](#id12)
+ [`encrypted`](#id13)
+ [`ebs_kms_key_id`](#id14)

Defines configuration settings for a RAID array that is built from a number of identical Amazon EBS volumes\. The RAID drive is mounted on the master node and is exported to compute nodes via NFS\.

The format is `[raid <raidname>]`\.

```
[raid rs]
shared_dir = raid
raid_type = 1
num_of_raid_volumes = 2
encrypted = true
```

## `shared_dir`<a name="id8"></a>

Defines the mount point for the RAID array on the master and compute nodes\.

The RAID drive is created only if this parameter is specified\.

Do not use `NONE` or `/NONE` as the shared directory\.

The following example mounts the array at `/raid`\.

```
shared_dir = raid
```

## `raid_type`<a name="raid-type"></a>

Defines the RAID type for the RAID array\.

The RAID drive is created only if this parameter is specified\.

Valid options are:
+ `0`
+ `1`

For more information on RAID types, see: [RAID info](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The following example creates a RAID `0` array:

```
raid_type = 0
```

## `num_of_raid_volumes`<a name="num-of-raid-volumes"></a>

Defines the number of Amazon EBS volumes to assemble the RAID array from\.

Minimum number of volumes = `2`\.

Maximum number of volumes = `5`\.

The default value is `2`\.

```
num_of_raid_volumes = 2
```

## `volume_type`<a name="id9"></a>

Defines the type of volume to build\.

Valid options are:
+ `gp2`
+ `io1`
+ `st1`
+ `sc1`

For more information, see [Amazon EBS Volume Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The default value is `gp2`\.

```
volume_type = io1
```

## `volume_size`<a name="id11"></a>

Defines the size of the volume to be created, in GiB\.

The default value is `20`\.

```
volume_size = 20
```

## `volume_iops`<a name="id12"></a>

Defines the number of IOPS for `io1` type volumes\.

```
volume_iops = 500
```

## `encrypted`<a name="id13"></a>

Specifies whether the file system is encrypted\.

The default value is `false`\.

```
encrypted = false
```

## `ebs_kms_key_id`<a name="id14"></a>

Specifies a custom AWS KMS key to use for encryption\.

This parameter must be used together with `encrypted = true`, and it must have a custom `ec2_iam_role`\.

For more information, see [Disk Encryption with a Custom KMS Key](tutorials_04_encrypted_kms_fs.md)\.

```
ebs_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```