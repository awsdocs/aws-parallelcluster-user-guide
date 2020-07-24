# `[fsx]` Section<a name="fsx-section"></a>

**Topics**
+ [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days)
+ [`copy_tags_to_backups`](#fsx-copy-tags-to-backups)
+ [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time)
+ [`deployment_type`](#fsx-deployment-type)
+ [`export_path`](#fsx-export-path)
+ [`fsx_backup_id`](#fsx-backup-id)
+ [`fsx_fs_id`](#fsx-fs-id)
+ [`fsx_kms_key_id`](#fsx-kms-key-id)
+ [`import_path`](#fsx-import-path)
+ [`imported_file_chunk_size`](#fsx-imported-file-chunk-size)
+ [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput)
+ [`shared_dir`](#fsx-shared-dir)
+ [`storage_capacity`](#fsx-storage-capacity)
+ [`weekly_maintenance_start_time`](#fsx-weekly-maintenance-start-time)

Defines configuration settings for an attached Amazon FSx for Lustre file system\. For more information about Amazon FSx for Lustre, see [Amazon FSx CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

Amazon FSx for Lustre is supported when `` is either `alinux`, `alinux2`, `centos7`, `ubuntu1604`, or `ubuntu1804`\.

When using Amazon Linux, the kernel must be >= `4.14.104-78.84.amzn1.x86_64`\. For detailed instructions, see [Installing the Lustre Client](https://docs.aws.amazon.com/fsx/latest/WindowsGuide/install-lustre-client.html) in the *Amazon FSx for Lustre User Guide*\.

**Note**  
Amazon FSx for Lustre is not currently supported when using `awsbatch` as a scheduler\.

**Note**  
Support for Amazon FSx for Lustre on `alinux2`, `ubuntu1604`, and `ubuntu1804` was added in AWS ParallelCluster 2\.6\.0\. Support for Amazon FSx for Lustre on `centos7` was added in AWS ParallelCluster 2\.4\.0\.

If using an existing file system, it must be associated to a security group that allows inbound TCP traffic to port `988`\. Setting the source to `0.0.0.0/0` on a security group rule provides client access from all IP ranges within your VPC security group for the protocol and port range for that rule\. To further limit access to your file systems we recommend using more restrictive sources for your security group rules, for example more specific CIDR ranges, IP addresses, or security group IDs\. This is done automatically when not using ``\.

To use an existing Amazon FSx file system, specify ``\.

The format is `[fsx <fsxname>]`\.

```
[fsx fs]
shared_dir = /fsx
fsx_fs_id = fs-073c3803dca3e28a6
```

To create and configure a new file system, use the following parameters:

```
[fsx fs]
shared_dir = /fsx
storage_capacity = 3600
imported_file_chunk_size = 1024
export_path = s3://bucket/folder
import_path = s3://bucket
weekly_maintenance_start_time = 1:00:00
```

## `automatic_backup_retention_days`<a name="fsx-automatic-backup-retention-days"></a>

**\(Optional\)** Specifies the number of days to retain automatic backups\. This is only valid for use with `PERSISTENT_1` deployment types\. When the `automatic_backup_retention_days` parameter is specified, the `export_path`, `import_path`, and `imported_file_chunk_size` parameters must not be specified\. This corresponds to the [AutomaticBackupRetentionDays](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-automaticbackupretentiondays) property\.

The default value is 0, which disables automatic backups\. The possible values are integers between 0 and 35, inclusive\.

```
automatic_backup_retention_days = 35
```

**Note**  
Support for `automatic_backup_retention_days` was added in AWS ParallelCluster 2\.8\.0\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `copy_tags_to_backups`<a name="fsx-copy-tags-to-backups"></a>

**\(Optional\)** Specifies whether tags for the filesystem are copied to the backups\. This is only valid for use with `PERSISTENT_1` deployment types\. When the `copy_tags_to_backups` parameter is specified, the `automatic_backup_retention_days` must be specified with a value greater than 0 and the `export_path`, `import_path`, and `imported_file_chunk_size` parameters must not be specified\. This corresponds to the [CopyTagsToBackups](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-copytagstobackups) property\.

The default value is `false`\.

```
copy_tags_to_backups = true
```

**Note**  
Support for `copy_tags_to_backups` was added in AWS ParallelCluster 2\.8\.0\.

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `daily_automatic_backup_start_time`<a name="fsx-daily-automatic-backup-start-time"></a>

**\(Optional\)** Specifies the time of day \(UTC\) to start automatic backups\. This is only valid for use with `PERSISTENT_1` deployment types\. When the `daily_automatic_backup_start_time` parameter is specified, the `automatic_backup_retention_days` must be specified with a value greater than 0, and the `export_path`, `import_path`, and `imported_file_chunk_size` parameters must not be specified\. This corresponds to the [DailyAutomaticBackupStartTime](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-dailyautomaticbackupstarttime) property\.

The format is `HH:MM`, where `HH` is the zero\-padded hour of the day \(0\-23\), and `MM` is the zero\-padded minute of the hour\. For example, 1:03 A\.M\. UTC would be:

```
daily_automatic_backup_start_time = 01:03
```

The default value is a random time between `00:00` and `23:59`\.

**Note**  
Support for `daily_automatic_backup_start_time` was added in AWS ParallelCluster 2\.8\.0\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `deployment_type`<a name="fsx-deployment-type"></a>

**\(Optional\)** Specifies the Amazon FSx for Lustre deployment type\. This corresponds to the [DeploymentType](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-deploymenttype) property\. For more information, see [Amazon FSx for Lustre Deployment Options](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html#file-system-options) in the *Amazon FSx for Lustre User Guide*\. Choose a scratch deployment type for temporary storage and shorter\-term processing of data\. `SCRATCH_2` is the latest generation of scratch file systems, and offers higher burst throughput over baseline throughput and also in\-transit encryption of data\.

The valid values are `SCRATCH_1`, `SCRATCH_2`, and `PERSISTENT_1`\.

`SCRATCH_1`  
The default deployment type for Amazon FSx for Lustre\. With this deployment type, the [`storage_capacity`](#fsx-storage-capacity) setting has possible values of 1200, 2400, and any multiple of 3600\.

`SCRATCH_2`  
The latest generation of scratch file systems that supports up to six times the baseline throughput for spiky workloads, and supports in\-transit encryption of data for supported instance types in supported regions\. For details, see [Encrypting data in transit](https://docs.aws.amazon.com/fsx/latest/LustreGuide/encryption-in-transit-fsxl.html) in the *Amazon FSx for Lustre User Guide*\. With this deployment type, the [`storage_capacity`](#fsx-storage-capacity) setting has possible values of 1200 and any multiple of 2400\.

`PERSISTENT_1`  
Designed for longer\-term storage\. The file servers are highly available and the data is replicated within the file systems' AWS Availability Zone \(AZ\), and supports in\-transit encryption of data for supported instance types\. With this deployment type, the [`storage_capacity`](#fsx-storage-capacity) setting has possible values of 1200 and any multiple of 2400\.

The default value is `SCRATCH_1`\.

```
deployment_type = SCRATCH_2
```

**Note**  
Support for `deployment_type` was added in AWS ParallelCluster 2\.6\.0\.

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `export_path`<a name="fsx-export-path"></a>

**\(Optional\)** Specifies the Amazon S3 path where the root of your file system is exported\. The path **must** be in the same Amazon S3 bucket as the `import_path` parameter\. When the `export_path` parameter is specified, the `automatic_backup_retention_days`, `copy_tags_to_backups`, `daily_automatic_backup_start_time`, and `fsx_backup_id` parameters must not be specified\. This corresponds to the [ExportPath](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-exportpath) property\.

The default value is `s3://import-bucket/FSxLustre[creation-timestamp]`, where `import-bucket` is the bucket provided in the `` parameter\.

```
export_path = s3://bucket/folder
```

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `fsx_backup_id`<a name="fsx-backup-id"></a>

**\(Optional\)** Specifies the ID of the backup to use for restoring the file system from an existing backup\. When the `fsx_backup_id` parameter is specified, the `deployment_type`, `export_path`, `fsx_kms_key_id`, `import_path`, `imported_file_chunk_size`, `storage_capacity`, and `per_unit_storage_throughput` parameters must not be specified, they are read from the backup\. Additionally, the `export_path`, `import_path`, and `imported_file_chunk_size` parameters must not be specified\.

This corresponds to the [BackupId](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-fsx-filesystem.html#cfn-fsx-filesystem-backupid) property\.

```
fsx_backup_id = backup-fedcba98
```

**Note**  
Support for `fsx_backup_id` was added in AWS ParallelCluster 2\.8\.0\.

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `fsx_fs_id`<a name="fsx-fs-id"></a>

**\(Optional\)** Attaches an existing Amazon FSx for Lustre file system\.

If this option is specified, only the [`shared_dir`](#fsx-shared-dir) and [`fsx_fs_id`](#fsx-fs-id) settings in the [[fsx] section](#fsx-section) are used and any other settings in the [[fsx] section](#fsx-section) are ignored\.

```
fsx_fs_id = fs-073c3803dca3e28a6
```

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `fsx_kms_key_id`<a name="fsx-kms-key-id"></a>

**\(Optional\)** Specifies the key ID of your AWS Key Management Service \(AWS KMS\) customer managed key\.

This key is used to encrypt the data in your file system at rest\.

This must be used with a custom `ec2_iam_role`\. For more information, see [Disk Encryption with a Custom KMS Key](tutorials_04_encrypted_kms_fs.md)\. This corresponds to the [KmsKeyId](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html#FSx-CreateFileSystem-request-KmsKeyId) parameter in the *Amazon FSx API Reference*\.

\.

```
fsx_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Note**  
Support for `fsx_kms_key_id` was added in AWS ParallelCluster 2\.6\.0\.

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `import_path`<a name="fsx-import-path"></a>

**\(Optional\)** Specifies the S3 bucket to load data from into the file system\. Also serves as the export bucket\. For more information, see ``\. When the `import_path` parameter is specified, the `automatic_backup_retention_days`, `copy_tags_to_backups`, `daily_automatic_backup_start_time`, and `fsx_backup_id` parameters must not be specified\. This corresponds to the [ImportPath](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystemLustreConfiguration.html#FSx-Type-CreateFileSystemLustreConfiguration-ImportPath) parameter in the *Amazon FSx API Reference*\.

Import occurs on cluster creation\. For more information, see [Importing Data from your Amazon S3 Bucket](https://docs.aws.amazon.com/fsx/latest/LustreGuide/fsx-data-repositories.html#import-data-repository) in the *Amazon FSx for Lustre User Guide*\.

If a value is not provided, the file system is empty\.

```
import_path =  s3://bucket
```

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `imported_file_chunk_size`<a name="fsx-imported-file-chunk-size"></a>

**\(Optional\)** Determines the stripe count and the maximum amount of data per file \(in MiB\) stored on a single physical disk, for files that are imported from a data repository \(using `import_path`\)\. The maximum number of disks that a single file can be striped across is limited by the total number of disks that make up the file system\. When the `imported_file_chunk_size` parameter is specified, the `automatic_backup_retention_days`, `copy_tags_to_backups`, `daily_automatic_backup_start_time`, and `fsx_backup_id` parameters must not be specified\.  This corresponds to the [ImportedFileChunkSize](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-importedfilechunksize) property\.

The chunk size default is `1024` \(1 GiB\), and it can go as high as 512,000 MiB \(500 GiB\)\. Amazon S3 objects have a maximum size of 5 TB\.

```
imported_file_chunk_size = 1024
```

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `per_unit_storage_throughput`<a name="fsx-per-unit-storage-throughput"></a>

**\(Required for `PERSISTENT_1` deployment types\)** For the ` = PERSISTENT_1` deployment type, describes the amount of read and write throughput for each 1 tebibyte \(TiB\) of storage, in MB/s/TiB\. File system throughput capacity is calculated by multiplying ﬁle system storage capacity \(TiB\) by the `per_unit_storage_throughput` \(MB/s/TiB\)\. For a 2\.4 TiB ﬁle system, provisioning 50 MB/s/TiB of `per_unit_storage_throughput` yields 120 MB/s of ﬁle system throughput\. You pay for the amount of throughput that you provision\. This corresponds to the [PerUnitStorageThroughput](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-perunitstoragethroughput) property\.

The possible values are 50, 100, 200\.

```
per_unit_storage_throughput = 200
```

**Note**  
Support for `per_unit_storage_throughput` was added in AWS ParallelCluster 2\.6\.0\.

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `shared_dir`<a name="fsx-shared-dir"></a>

 **\(Required\)** Defines the mount point for the Amazon FSx for Lustre file system on the master and compute nodes\.

Do not use `NONE` or `/NONE` as the shared directory\.

The following example mounts the file system at `/fsx`\.

```
shared_dir = /fsx
```

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `storage_capacity`<a name="fsx-storage-capacity"></a>

**\(Required\)** Specifies the storage capacity of the file system, in GiB\. This corresponds to the [StorageCapacity](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-fsx-filesystem.html#cfn-fsx-filesystem-storagecapacity) property\.

The storage capacity possible values vary based on the [`deployment_type`](#fsx-deployment-type) setting\.

`SCRATCH_1`  
The possible values are 1200, 2400, and any multiple of 3600\.

`SCRATCH_2` and `PERSISTENT_1`  
The possible values are 1200 and any multiple of 2400\.

```
storage_capacity = 7200
```

**Note**  
For AWS ParallelCluster 2\.5\.0 and 2\.5\.1, `storage_capacity` supported possible values of 1200, 2400, and any multiple of 3600\. For versions earlier than AWS ParallelCluster 2\.5\.0, `storage_capacity` had a minimum size of 3600\.

[Update policy: If this setting is changed, the update will fail.](using-pcluster-update.md#update-policy-fail)

## `weekly_maintenance_start_time`<a name="fsx-weekly-maintenance-start-time"></a>

**\(Optional\)** Specifies a preferred time to perform weekly maintenance, in the UTC time zone\. This corresponds to the [WeeklyMaintenanceStartTime](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-weeklymaintenancestarttime) property\.

The format is \[day of week\]:\[hour of day\]:\[minute of hour\]\. For example, Monday at Midnight is:

```
weekly_maintenance_start_time = 1:00:00
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)