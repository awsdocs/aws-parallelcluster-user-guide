# `SharedStorage` section<a name="SharedStorage-v3"></a>

**\(Optional\)** The shared storage settings for the cluster\. Up to 5 Amazon EBS, 1 Amazon EFS, and 1 FSx for Lustre file systems can be specified\.

**Note**  
If AWS Batch is used as a scheduler, FSx for Lustre is only available on the cluster head node\.

```
SharedStorage:
  - MountDir: string
    Name: string
    StorageType: Ebs
    EbsSettings:
      VolumeType: string
      Iops: integer
      Size: integer
      Encrypted: boolean
      KmsKeyId: string
      SnapshotId: string
      Throughput: integer
      VolumeId: string
      DeletionPolicy: string
      Raid:
        Type: string
        NumberOfVolumes: integer
  - MountDir: string
    Name: string
    StorageType: Efs
    EfsSettings:
      ThroughputMode: string
      ProvisionedThroughput: integer
      Encrypted: boolean
      KmsKeyId: string
      PerformanceMode: string
      FileSystemId: string
  - MountDir: string
    Name: string
    StorageType: FsxLustre
    FsxLustreSettings:
      StorageCapacity: integer
      DeploymentType: string
      ImportedFileChunkSize: integer
      DataCompressionType: string
      ExportPath: string
      ImportPath: string
      WeeklyMaintenanceStartTime: string
      AutomaticBackupRetentionDays: integer
      CopyTagsToBackups: boolean
      DailyAutomaticBackupStartTime: string
      PerUnitStorageThroughput: integer
      BackupId: string
      KmsKeyId: string
      FileSystemId: string
      AutoImportPolicy: string
      DriveCacheType: string
      StorageType: string
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `SharedStorage` Properties<a name="SharedStorage-v3.properties"></a>

`MountDir` \(**Required**, `String`\)  
The path where the shared storage is mounted\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`Name` \(**Required**, `String`\)  
The name of the shared storage\. This name is used when updating the settings\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`StorageType` \(**Required**, `String`\)  
The type of the shared storage\. Supported values are `Ebs`, `Efs`, and `FsxLustre`\.  
If AWS Batch is used as a scheduler, FSx for Lustre is only available on the cluster head node\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `EbsSettings`<a name="SharedStorage-v3-EbsSettings"></a>

**\(Optional\)** The settings for an Amazon EBS volume\.

```
EbsSettings:
  VolumeType: string
  Iops: integer
  Size: integer
  Encrypted: boolean
  KmsKeyId: string
  SnapshotId: string
  VolumeId: string
  Throughput: integer
  DeletionPolicy: string
  Raid:
    Type: string
    NumberOfVolumes: integer
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-ignored-v3)

### `EbsSettings` Properties<a name="SharedStorage-v3-EbsSettings.properties"></a>

`VolumeType` \(**Optional**, `String`\)  
Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)\. Supported values are `gp2`, `gp3`, `io1`, `io2`, `sc1`, `st1`, and `standard`\. The default value is `gp2`\.  
For more information, see [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`Iops` \(**Optional**, `Integer`\)  
Defines the number of IOPS for `io1`, `io2`, and `gp3` type volumes\.  
The default value, supported values, and `volume_iops` to `volume_size` ratio varies by `VolumeType` and `Size`\.    
`VolumeType` = `io1`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 †  
Maximum `volume_iops` to `volume_size` ratio = 50 IOPS per GiB\. 5000 IOPS requires a `volume_size` of at least 100 GiB\.  
`VolumeType` = `io2`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 \(256000 for `io2` Block Express volumes\) †  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 10 GiB\.  
`VolumeType` = `gp3`  
Default `Iops` = 3000  
Supported values `Iops` = 3000–16000  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 10 GiB\.
† Maximum IOPS is guaranteed only on [Instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS\. Older `io1` volumes might not reach full performance unless you [modify the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\. `io2` Block Express volumes support `volume_iops` values up to 256000 on `R5b` instance types\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`Size` \(**Optional**, `Integer`\)  
Specifies the volume size in gibibytes \(GiB\)\. The default value is 35\.   
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`Encrypted` \(**Optional**, `Boolean`\)  
Specifies if the volume is encrypted\. The default value is `true`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`KmsKeyId` \(**Optional**, `String`\)  
Specifies a custom AWS KMS key to use for encryption\. This setting requires that the `Encrypted` setting is set to `true`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`SnapshotId` \(**Optional**, `String`\)  
Specifies the Amazon EBS snapshot ID if you're using a snapshot as the source for the volume\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`VolumeId` \(**Optional**, `String`\)  
Specifies the Amazon EBS volume ID\. When this is specified for an `EbsSettings` instance, only the `MountDir` parameter can also be specified\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`Throughput` \(**Required**, `Integer`\)  
The throughput, in MiB/s to provision for a volume, with a maximum of 1,000 MiB/s\.  
This setting is valid only when `VolumeType` is `gp3`\. The supported range is 125 to 1000, with a default value of 125\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`DeletionPolicy` \(**Required**, `String`\)  
Specifies whether the volume should be retained, deleted, or snapshotted when the cluster is deleted\. The supported values are `Delete`, `Retain`, and `Snapshot`\. The default value is `Delete`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

### `Raid`<a name="SharedStorage-v3-EbsSettings-Raid"></a>

**\(Optional\)** Defines the configuration of a RAID volume\.

```
Raid:
  Type: string
  NumberOfVolumes: integer
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

#### `Raid` Properties<a name="SharedStorage-v3-EbsSettings-Raid.properties"></a>

`Type` \(**Required**, `String`\)  
Defines the type of RAID array\. Supported values are "0" \(striped\) and "1" \(mirrored\)\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`NumberOfVolumes` \(**Optional**, `Integer`\)  
Defines the number of Amazon EBS volumes to use to create the RAID array\. The supported range of values is 2\-5\. The default value \(when the `Raid` setting is defined\) is 2\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `EfsSettings`<a name="SharedStorage-v3-EfsSettings"></a>

**\(Optional\)** The settings for an Amazon EFS file system\.

```
EfsSettings:
  Encrypted: boolean
  KmsKeyId: string
  PerformanceMode: string
  ThroughputMode: string
  ProvisionedThroughput: integer
  FileSystemId: string
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

### `EfsSettings` Properties<a name="SharedStorage-v3-EfsSettings.properties"></a>

`Encrypted` \(**Optional**, `Boolean`\)  
Specifies if the Amazon EFS file system is encrypted\. The default value is `false`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`KmsKeyId` \(**Optional**, `String`\)  
Specifies a custom AWS KMS key to use for encryption\. This setting requires that the `Encrypted` setting is set to `true`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`PerformanceMode` \(**Optional**, `String`\)  
Specifies the performance mode of the Amazon EFS file system\. Supported values are `generalPurpose` and `maxIO`\. The default value is `generalPurpose`\. For more information, see [Performance modes](https://docs.aws.amazon.com/efs/latest/ug/performance.html#performancemodes) in the *Amazon Elastic File System User Guide*\.  
We recommend the `generalPurpose` performance mode for most file systems\.  
File systems that use the `maxIO` performance mode can scale to higher levels of aggregate throughput and operations per second\. However, there is a trade\-off of slightly higher latencies for most file operations\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`ThroughputMode` \(**Optional**, `String`\)  
Specifies the throughput mode of the Amazon EFS file system\. Supported values are `bursting` and `provisioned`\. The default value is `bursting`\. When `provisioned` is used, `ProvisionedThroughput` must be specified\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`ProvisionedThroughput` \(**Required** when `ThroughputMode` is `provisioned`, `Integer`\)  
Defines the provisioned throughput \(in MiB/s\) of the Amazon EFS file system, measured in MiB/s\. This corresponds to the [ProvisionedThroughputInMibps](https://docs.aws.amazon.com/efs/latest/ug/API_CreateFileSystem.html#efs-CreateFileSystem-response-ProvisionedThroughputInMibps) parameter in the *Amazon EFS API Reference*\.  
If you use this parameter, you must set `ThroughputMode` to `provisioned`\.  
The supported range is `1`\-`1024`\. To request a limit increase, contact AWS Support\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`FileSystemId` \(**Optional**, `String`\)  
Defines the Amazon EFS file system ID for an existing file system\.  
When this is specified, only `MountDir` can be specified\. No other `EfsSettings` can be specified\.  
If you set this option, it only supports file systems:  
+ That don't have a mount target in the stack's Availability Zone

  OR
+ That do have an existing mount target in the stack's Availability Zone, with inbound and outbound NFS traffic allowed from `0.0.0.0/0`\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `FsxLustreSettings`<a name="SharedStorage-v3-FsxLustreSettings"></a>

**\(Optional\)** The settings for an FSx for Lustre file system\.

```
FsxLustreSettings:
  StorageCapacity: integer
  DeploymentType: string
  ImportedFileChunkSize: integer
  DataCompressionType: string
  ExportPath: string
  ImportPath: string
  WeeklyMaintenanceStartTime: string
  AutomaticBackupRetentionDays: integer
  CopyTagsToBackups: boolean
  DailyAutomaticBackupStartTime: string
  PerUnitStorageThroughput: integer
  BackupId: string # BackupId cannot coexist with some of the fields
  KmsKeyId: string
  FileSystemId: string # FileSystemId cannot coexist with other fields
  AutoImportPolicy: string
  DriveCacheType: string
  StorageType: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

**Note**  
If AWS Batch is used as a scheduler, FSx for Lustre is only available on the cluster head node\.

### `FsxLustreSettings` Properties<a name="SharedStorage-v3-FsxLustreSettings.properties"></a>

`StorageCapacity` \(**Required**, `Integer`\)  
Sets the storage capacity of the FSx for Lustre file system, in GiB\. `StorageCapacity` is required if you are creating a new file system\. Do not include `StorageCapacity` if `BackupId` or `FileSystemId` is specified\.  
+ For `SCRATCH_2` and `PERSISTENT_1` deployment types, valid values are 1200 GiB, 2400 GiB, and increments of 2400 GiB\.
+ For `SCRATCH_1` deployment type, valid values are 1200 GiB, 2400 GiB, and increments of 3600 GiB\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`DeploymentType` \(**Optional**, `String`\)  
Specifies the deployment type of the FSx for Lustre file system\. Supported values are `SCRATCH_1`, `SCRATCH_2`, and `PERSISTENT_1`\. The default value is `SCRATCH_1`\.  
 Choose `SCRATCH_1` and `SCRATCH_2` deployment types when you need temporary storage and shorter\-term processing of data\. The `SCRATCH_2` deployment type provides in\-transit encryption of data and higher burst throughput capacity than `SCRATCH_1`\.  
Choose `PERSISTENT_1` deployment type for longer\-term storage and workloads and encryption of data in transit\. To learn more about deployment types, see [ FSx for Lustre Deployment Options](https://docs.aws.amazon.com/fsx/latest/LustreGuide/lustre-deployment-types.html) in the *Amazon FSx for Lustre User Guide*\.  
Encryption of data in\-transit is automatically enabled when you access a `SCRATCH_2` or `PERSISTENT_1` file system from Amazon EC2 instances that [support this feature](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html)\. \(Default = `SCRATCH_1`\)   
Encryption of data in\-transit for `SCRATCH_2` and `PERSISTENT_1` deployment types is supported when accessed from supported instance types in supported AWS Regions\. To learn more, [Encrypting data in transit](https://docs.aws.amazon.com/fsx/latest/LustreGuide/encryption-in-transit-fsxl.html) in the *Amazon FSx for Lustre User Guide*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`ImportedFileChunkSize` \(**Optional**, `Integer`\)  
For files imported from a data repository, this value determines the stripe count and maximum amount of data per file \(in MiB\) stored on a single physical disk\. The maximum number of disks that a single file can be striped across is limited by the total number of disks that make up the file system\.  
The default chunk size is 1,024 MiB \(1 GiB\) and can go as high as 512,000 MiB \(500 GiB\)\. Amazon S3 objects have a maximum size of 5 TB\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`DataCompressionType` \(**Optional**, `String`\)  
Sets the data compression configuration for the FSx for Lustre file system\. The supported value is `LZ4`, which indicates that data compression is turned on with the LZ4 algorithm\. When `DataCompressionType` is not specified, data compression is turned off when the file system is created\.  
For more information, see [Lustre data compression](https://docs.aws.amazon.com/fsx/latest/LustreGuide/data-compression.html)\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`ExportPath` \(**Optional**, `String`\)  
The path in Amazon S3 where the root of your FSx for Lustre file system is exported\. This setting is only supported when the `ImportPath` parameter is specified\. The path must use the same Amazon S3 bucket as specified in `ImportPath`\. You can provide an optional prefix to which new and changed data is to be exported from your FSx for Lustre file system\. If an `ExportPath` value is not provided, FSx for Lustre sets a default export path, `s3://import-bucket/FSxLustre[creation-timestamp]`\. The timestamp is in UTC format, for example `s3://import-bucket/FSxLustre20181105T222312Z`\.  
The Amazon S3 export bucket must be the same as the import bucket specified by `ImportPath`\. If you only specify a bucket name, such as `s3://import-bucket`, you get a 1:1 mapping of file system objects to Amazon S3 bucket objects\. This mapping means that the input data in Amazon S3 is overwritten on export\. If you provide a custom prefix in the export path, such as `s3://import-bucket/[custom-optional-prefix]`, FSx for Lustre exports the contents of your file system to that export prefix in the Amazon S3 bucket\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`ImportPath` \(**Optional**, `String`\)  
The path to the Amazon S3 bucket \(including the optional prefix\) that you're using as the data repository for your FSx for Lustre file system\. The root of your FSx for Lustre file system will be mapped to the root of the Amazon S3 bucket you select\. An example is `s3://import-bucket/optional-prefix`\. If you specify a prefix after the Amazon S3 bucket name, only object keys with that prefix are loaded into the file system\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`WeeklyMaintenanceStartTime` \(**Optional**, `String`\)  
The preferred start time to perform weekly maintenance, formatted `"d:HH:MM"` in the UTC time zone, where `d` is the weekday number, from 1 through 7, beginning with Monday and ending with Sunday\.Double quote is required for this filed\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`AutomaticBackupRetentionDays` \(**Optional**, `Integer`\)  
The number of days to retain automatic backups\. Setting this to 0 disables automatic backups\. The supported range is 0\-90\. The default is 0\. Only valid for use with `PERSISTENT_1` deployment types\. For more information, see [Working with backups](https://docs.aws.amazon.com/fsx/latest/LustreGuide/using-backups-fsx.html) in the *Amazon FSx for Lustre User Guide*\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`CopyTagsToBackups` \(**Optional**, `Boolean`\)  
If `true`, tags for the FSx for Lustre file system should be copied to backups\. This value defaults to `false`\. If it's set to `true`, all tags for the file system are copied to all automatic and user\-initiated backups where the user doesn't specify tags\. If this value is `true`, and you specify one or more tags, only the specified tags are copied to backups\. If you specify one or more tags when creating a user\-initiated backup, no tags are copied from the file system, regardless of this value\. Only valid for use with `PERSISTENT_1` deployment types\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`DailyAutomaticBackupStartTime` \(**Optional**, `String`\)  
A recurring daily time, in the format`HH:MM`\. `HH` is the zero\-padded hour of the day \(00\-23\), and `MM` is the zero\-padded minute of the hour \(00\-59\)\. For example, `05:00` specifies 5 A\.M\. daily\. Only valid for use with `PERSISTENT_1` deployment types\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`PerUnitStorageThroughput` \(**Required for `PERSISTENT_1` deployment types**, `Integer`\)  
Describes the amount of read and write throughput for each 1 tebibyte of storage, in MB/s/TiB\. File system throughput capacity is calculated by multiplying ﬁle system storage capacity \(TiB\) by the `PerUnitStorageThroughput` \(MB/s/TiB\)\. For a 2\.4 TiB ﬁle system, provisioning 50 MB/s/TiB of `PerUnitStorageThroughput` yields 120 MB/s of ﬁle system throughput\. You pay for the amount of throughput that you provision\. This corresponds to the [PerUnitStorageThroughput](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-perunitstoragethroughput) property\.  
The possible values depend on the value of the `StorageType` setting\.    
`StorageType` = `SSD`  
The possible values are 50, 100, 200\.  
`StorageType` = `HDD`  
The possible values are 12, 40\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`BackupId` \(**Optional**, `String`\)  
Specifies the ID of the backup to use for restoring the FSx for Lustre file system from an existing backup\. When the `BackupId` setting is specified, the `AutoImportPolicy`, `DeploymentType`, `ExportPath`, `KmsKeyId`, `ImportPath`, `ImportedFileChunkSize`, `StorageCapacity`, and `PerUnitStorageThroughput` settings must not be specified\. These settings are read from the backup\. Additionally, the `AutoImportPolicy`, `ExportPath`, `ImportPath`, and `ImportedFileChunkSize` settings must not be specified\. This corresponds to the [BackupId](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-fsx-filesystem.html#cfn-fsx-filesystem-backupid) property\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`KmsKeyId` \(**Optional**, `String`\)  
The ID of the AWS Key Management Service \(AWS KMS\) key used to encrypt the FSx for Lustre file system's data for persistent FSx for Lustre file systems at rest\. If not specified, the FSx for Lustre managed key is used\. The `SCRATCH_1` and `SCRATCH_2` FSx for Lustre file systems are always encrypted at rest using FSx for Lustre managed keys\. For more information, see [Encrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) in the *AWS Key Management Service API Reference*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`FileSystemId` \(**Optional**, `String`\)  
Specifies the id of an existing FSx for Lustre file system\.  
If this option is specified, only the `MountDir` and `FileSystemId` settings in the `FsxLustreSettings` are used and any other settings in the `FsxLustreSettings` are ignored\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`AutoImportPolicy` \(**Optional**, `String`\)  
When you create your FSx for Lustre file system, your existing Amazon S3 objects appear as file and directory listings\. Use this property to choose how FSx for Lustre keeps your file and directory listings up to date as you add or modify objects in your linked Amazon S3 bucket\. `AutoImportPolicy` can have the following values:  
+  `NEW` \- Automatic import is on\. FSx for Lustre automatically imports directory listings of any new objects added to the linked Amazon S3 bucket that do not currently exist in the FSx for Lustre file system\. 
+  `NEW_CHANGED` \- Automatic import is on\. FSx for Lustre automatically imports file and directory listings of any new objects added to the Amazon S3 bucket and any existing objects that are changed in the Amazon S3 bucket after you choose this option\. 
+  `NEW_CHANGED_DELETED` \- Automatic import is on\. FSx for Lustre automatically imports file and directory listings of any new objects added to the Amazon S3 bucket, any existing objects that are changed in the Amazon S3 bucket, and any objects that were deleted in the Amazon S3 bucket after you choose this option\.
**Note**  
Support for `NEW_CHANGED_DELETED` was added in AWS ParallelCluster version 3\.1\.1\.
If `AutoImportPolicy` is not specified, automatic import is off\. FSx for Lustre only updates file and directory listings from the linked Amazon S3 bucket when the file system is created\. FSx for Lustre does not update file and directory listings for any new or changed objects after choosing this option\.  
For more information, see [Automatically import updates from your S3 bucket](https://docs.aws.amazon.com/fsx/latest/LustreGuide/autoimport-data-repo.html) in the *Amazon FSx for Lustre User Guide*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`DriveCacheType` \(**Optional**, `String`\)  
Specifies that the file system has an SSD drive cache\. This can only be set if the `StorageType` setting is set to `HDD`, and the `DeploymentType` setting is set to `PERSISTENT_1`\. This corresponds to the [DriveCacheType](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-drivecachetype) property\. For more information, see [FSx for Lustre deployment options](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html#file-system-options) in the *Amazon FSx for Lustre User Guide*\.  
The only valid value is `READ`\. To disable the SSD drive cache, don’t specify the `DriveCacheType` setting\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`StorageType` \(**Optional**, `String`\)  
Sets the storage type for the FSx for Lustre file system you're creating\. Valid values are `SSD` and `HDD`\.  
+ Set to `SSD` to use solid state drive storage\.
+ Set to `HDD` to use hard disk drive storage\. `HDD` is supported on `PERSISTENT` deployment types\. 
The default value is `SSD`\. For more information, see [ Storage Type Options](https://docs.aws.amazon.com/fsx/latest/WindowsGuide/optimize-fsx-costs.html#storage-type-options) in the *Amazon FSx for Windows User Guide* and [Multiple Storage Options](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html#storage-options) in the *Amazon FSx for Lustre User Guide*\.   
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)
