# `[fsx]` section<a name="fsx-section"></a>

**Topics**
+ [`auto_import_policy`](#fsx-auto-import-policy)
+ [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days)
+ [`copy_tags_to_backups`](#fsx-copy-tags-to-backups)
+ [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time)
+ [`deployment_type`](#fsx-deployment-type)
+ [`drive_cache_type`](#fsx-drive-cache-type)
+ [`export_path`](#fsx-export-path)
+ [`fsx_backup_id`](#fsx-backup-id)
+ [`fsx_fs_id`](#fsx-fs-id)
+ [`fsx_kms_key_id`](#fsx-kms-key-id)
+ [`import_path`](#fsx-import-path)
+ [`imported_file_chunk_size`](#fsx-imported-file-chunk-size)
+ [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput)
+ [`shared_dir`](#fsx-shared-dir)
+ [`storage_capacity`](#fsx-storage-capacity)
+ [`storage_type`](#fsx-storage-type)
+ [`weekly_maintenance_start_time`](#fsx-weekly-maintenance-start-time)

Defines configuration settings for an attached Amazon FSx for Lustre file system\. For more information about Amazon FSx for Lustre, see [Amazon FSx CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

Amazon FSx for Lustre is supported if the [`base_os`](cluster-definition.md#base-os) is `alinux`, `alinux2`, `centos7`, `centos8`, `ubuntu1604`, or `ubuntu1804`\.

When using Amazon Linux, the kernel must be >= `4.14.104-78.84.amzn1.x86_64`\. For detailed instructions, see [Installing the lustre client](https://docs.aws.amazon.com/fsx/latest/WindowsGuide/install-lustre-client.html) in the *Amazon FSx for Lustre User Guide*\.

**Note**  
Amazon FSx for Lustre isn't currently supported when using `awsbatch` as a scheduler\.

**Note**  
Support for Amazon FSx for Lustre on `centos8` was added in AWS ParallelCluster version 2\.10\.0\.Support for Amazon FSx for Lustre on `alinux2`, `ubuntu1604`, and `ubuntu1804` was added in AWS ParallelCluster version 2\.6\.0\. Support for Amazon FSx for Lustre on `centos7` was added in AWS ParallelCluster version 2\.4\.0\.

If using an existing file system, it must be associated to a security group that allows inbound TCP traffic to port `988`\. Setting the source to `0.0.0.0/0` on a security group rule provides client access from all the IP ranges within your VPC security group for the protocol and port range for that rule\. To further limit access to your file systems, we recommend using more restrictive sources for your security group rules\. For example, you can use more specific CIDR ranges, IP addresses, or security group IDs\. This is done automatically when not using [`vpc_security_group_id`](vpc-section.md#vpc-security-group-id)\.

To use an existing Amazon FSx file system, specify [`fsx_fs_id`](#fsx-fs-id)\.

The format is `[fsx fsx-name]`\. *fsx\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

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

## `auto_import_policy`<a name="fsx-auto-import-policy"></a>

**\(Optional\)** Specifies the automatic import policy for reflecting changes in the S3 bucket used to create the Amazon FSx for Lustre file system\. The possible values are the following:

`NEW`  
Amazon FSx for Lustre automatically imports directory listings of any new objects added to the linked S3 bucket that don't currently exist in the Amazon FSx for Lustre file system\. 

`NEW_CHANGED`  
Amazon FSx for Lustre automatically imports file and directory listings of any new objects added to the S3 bucket and any existing objects that are changed in the S3 bucket\. 

This corresponds to the [AutoImportPolicy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-autoimportpolicy) property\. For more information, see [Automatically import updates from your S3 bucket](https://docs.aws.amazon.com/fsx/latest/LustreGuide/autoimport-data-repo.html) in the *Amazon FSx for Lustre User Guide* When the [`auto_import_policy`](#fsx-auto-import-policy) parameter is specified, the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days), [`copy_tags_to_backups`](#fsx-copy-tags-to-backups), [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time), and [`fsx_backup_id`](#fsx-backup-id) parameters must not be specified\.

The default value, if the `auto_import_policy` setting isn't specified, is `NONE`\. This setting disables automatic imports\. Amazon FSx for Lustre only updates file and directory listings from the linked S3 bucket when the file system is created\.

```
auto_import_policy = NEW_CHANGED
```

**Note**  
Support for [`auto_import_policy`](#fsx-auto-import-policy) was added in AWS ParallelCluster version 2\.10\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `automatic_backup_retention_days`<a name="fsx-automatic-backup-retention-days"></a>

**\(Optional\)** Specifies the number of days to retain automatic backups\. This is only valid for use with `PERSISTENT_1` deployment types\. When the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days) parameter is specified, the [`auto_import_policy`](#fsx-auto-import-policy), [`export_path`](#fsx-export-path), [`import_path`](#fsx-import-path), and [`imported_file_chunk_size`](#fsx-imported-file-chunk-size) parameters must not be specified\. This corresponds to the [AutomaticBackupRetentionDays](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-automaticbackupretentiondays) property\.

The default value is 0\. This setting disables automatic backups\. The possible values are integers between 0 and 35, inclusive\.

```
automatic_backup_retention_days = 35
```

**Note**  
Support for [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days) was added in AWS ParallelCluster version 2\.8\.0\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `copy_tags_to_backups`<a name="fsx-copy-tags-to-backups"></a>

**\(Optional\)** Specifies whether tags for the filesystem are copied to the backups\. This is only valid for use with `PERSISTENT_1` deployment types\. When the [`copy_tags_to_backups`](#fsx-copy-tags-to-backups) parameter is specified, the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days) must be specified with a value greater than 0, and the [`auto_import_policy`](#fsx-auto-import-policy), [`export_path`](#fsx-export-path), [`import_path`](#fsx-import-path), and [`imported_file_chunk_size`](#fsx-imported-file-chunk-size) parameters must not be specified\. This corresponds to the [CopyTagsToBackups](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-copytagstobackups) property\.

The default value is `false`\.

```
copy_tags_to_backups = true
```

**Note**  
Support for [`copy_tags_to_backups`](#fsx-copy-tags-to-backups) was added in AWS ParallelCluster version 2\.8\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `daily_automatic_backup_start_time`<a name="fsx-daily-automatic-backup-start-time"></a>

**\(Optional\)** Specifies the time of day \(UTC\) to start automatic backups\. This is only valid for use with `PERSISTENT_1` deployment types\. When the [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time) parameter is specified, the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days) must be specified with a value greater than 0, and the [`auto_import_policy`](#fsx-auto-import-policy), [`export_path`](#fsx-export-path), [`import_path`](#fsx-import-path), and [`imported_file_chunk_size`](#fsx-imported-file-chunk-size) parameters must not be specified\. This corresponds to the [DailyAutomaticBackupStartTime](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-dailyautomaticbackupstarttime) property\.

The format is `HH:MM`, where `HH` is the zero\-padded hour of the day \(0\-23\), and `MM` is the zero\-padded minute of the hour\. For example, 1:03 A\.M\. UTC would be:

```
daily_automatic_backup_start_time = 01:03
```

The default value is a random time between `00:00` and `23:59`\.

**Note**  
Support for [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time) was added in AWS ParallelCluster version 2\.8\.0\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `deployment_type`<a name="fsx-deployment-type"></a>

**\(Optional\)** Specifies the Amazon FSx for Lustre deployment type\. This corresponds to the [DeploymentType](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-deploymenttype) property\. For more information, see [Amazon FSx for Lustre deployment options](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html#file-system-options) in the *Amazon FSx for Lustre User Guide*\. Choose a scratch deployment type for temporary storage and shorter\-term processing of data\. `SCRATCH_2` is the latest generation of scratch file systems\. It offers higher burst throughput over baseline throughput and the in\-transit encryption of data\.

The valid values are `SCRATCH_1`, `SCRATCH_2`, and `PERSISTENT_1`\.

`SCRATCH_1`  
The default deployment type for Amazon FSx for Lustre\. With this deployment type, the [`storage_capacity`](#fsx-storage-capacity) setting has possible values of 1200, 2400, and any multiple of 3600\. Support for `SCRATCH_1` was added in AWS ParallelCluster version 2\.4\.0\.

`SCRATCH_2`  
The latest generation of scratch file systems\. It supports up to six times the baseline throughput for spiky workloads\. It also supports in\-transit encryption of data for supported instance types in supported Regions\. For more information, see [Encrypting data in transit](https://docs.aws.amazon.com/fsx/latest/LustreGuide/encryption-in-transit-fsxl.html) in the *Amazon FSx for Lustre User Guide*\. With this deployment type, the [`storage_capacity`](#fsx-storage-capacity) setting has possible values of 1200 and any multiple of 2400\. Support for `SCRATCH_2` was added in AWS ParallelCluster version 2\.6\.0\.

`PERSISTENT_1`  
Designed for longer\-term storage\. The file servers are highly available and the data is replicated within the file systems' AWS Availability Zone,\. It supports in\-transit encryption of data for supported instance types\. With this deployment type, the [`storage_capacity`](#fsx-storage-capacity) setting has possible values of 1200 and any multiple of 2400\. Support for `PERSISTENT_1` was added in AWS ParallelCluster version 2\.6\.0\.

The default value is `SCRATCH_1`\.

```
deployment_type = SCRATCH_2
```

**Note**  
Support for [`deployment_type`](#fsx-deployment-type) was added in AWS ParallelCluster version 2\.6\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `drive_cache_type`<a name="fsx-drive-cache-type"></a>

**\(Optional\)** Specifies that the file system has an SSD drive cache\. This can only be set if the [`storage_type`](#fsx-storage-type) setting is set to `HDD`\. This corresponds to the [DriveCacheType](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-drivecachetype) property\. For more information, see [Amazon FSx for Lustre deployment options](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html#file-system-options) in the *Amazon FSx for Lustre User Guide*\.

The only valid value is `READ`\. To disable the SSD drive cache, don’t specify the `drive_cache_type` setting\.

```
drive_cache_type = READ
```

**Note**  
Support for [`drive_cache_type`](#fsx-drive-cache-type) was added in AWS ParallelCluster version 2\.10\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `export_path`<a name="fsx-export-path"></a>

**\(Optional\)** Specifies the Amazon S3 path where the root of your file system is exported\. The path **must** be in the same Amazon S3 bucket as the [`import_path`](#fsx-import-path) parameter\. When the [`export_path`](#fsx-export-path) parameter is specified, the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days), [`copy_tags_to_backups`](#fsx-copy-tags-to-backups), [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time), and [`fsx_backup_id`](#fsx-backup-id) parameters must not be specified\. This corresponds to the [ExportPath](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-exportpath) property\. File data and metadata isn't automatically exported to the `export_path`\. For information about exporting data and metadata, see [Using Data Repository Tasks to Export Data and Metadata Changes](https://docs.aws.amazon.com/fsx/latest/LustreGuide/export-data-repo-task.html) in the *Amazon FSx for Lustre User Guide*\.

The default value is `s3://import-bucket/FSxLustre[creation-timestamp]`, where `import-bucket` is the bucket provided in the [`import_path`](#fsx-import-path) parameter\.

```
export_path = s3://bucket/folder
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `fsx_backup_id`<a name="fsx-backup-id"></a>

**\(Optional\)** Specifies the ID of the backup to use for restoring the file system from an existing backup\. When the [`fsx_backup_id`](#fsx-backup-id) parameter is specified, the [`auto_import_policy`](#fsx-auto-import-policy), [`deployment_type`](#fsx-deployment-type), [`export_path`](#fsx-export-path), [`fsx_kms_key_id`](#fsx-kms-key-id), [`import_path`](#fsx-import-path), [`imported_file_chunk_size`](#fsx-imported-file-chunk-size), [`storage_capacity`](#fsx-storage-capacity), and [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput) parameters must not be specified\. These parameters are read from the backup\. Additionally, the [`auto_import_policy`](#fsx-auto-import-policy), [`export_path`](#fsx-export-path), [`import_path`](#fsx-import-path), and [`imported_file_chunk_size`](#fsx-imported-file-chunk-size) parameters must not be specified\.

This corresponds to the [BackupId](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-fsx-filesystem.html#cfn-fsx-filesystem-backupid) property\.

```
fsx_backup_id = backup-fedcba98
```

**Note**  
Support for [`fsx_backup_id`](#fsx-backup-id) was added in AWS ParallelCluster version 2\.8\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `fsx_fs_id`<a name="fsx-fs-id"></a>

**\(Optional\)** Attaches an existing Amazon FSx for Lustre file system\.

If this option is specified, only the [`shared_dir`](#fsx-shared-dir) and [`fsx_fs_id`](#fsx-fs-id) settings in the [`[fsx]` section](#fsx-section) are used and any other settings in the [`[fsx]` section](#fsx-section) are ignored\.

```
fsx_fs_id = fs-073c3803dca3e28a6
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `fsx_kms_key_id`<a name="fsx-kms-key-id"></a>

**\(Optional\)** Specifies the key ID of your AWS Key Management Service \(AWS KMS\) customer managed key\.

This key is used to encrypt the data in your file system at rest\.

This must be used with a custom [`ec2_iam_role`](cluster-definition.md#ec2-iam-role)\. For more information, see [Disk encryption with a custom KMS Key](tutorials_04_encrypted_kms_fs.md)\. This corresponds to the [KmsKeyId](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html#FSx-CreateFileSystem-request-KmsKeyId) parameter in the *Amazon FSx API Reference*\.

\.

```
fsx_kms_key_id = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Note**  
Support for [`fsx_kms_key_id`](#fsx-kms-key-id) was added in AWS ParallelCluster version 2\.6\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `import_path`<a name="fsx-import-path"></a>

**\(Optional\)** Specifies the S3 bucket to load data from into the file system and serve as the export bucket\. For more information, see [`export_path`](#fsx-export-path)\. If you specify the [`import_path`](#fsx-import-path) parameter, the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days), [`copy_tags_to_backups`](#fsx-copy-tags-to-backups), [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time), and [`fsx_backup_id`](#fsx-backup-id) parameters must not be specified\. This corresponds to the [ImportPath](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystemLustreConfiguration.html#FSx-Type-CreateFileSystemLustreConfiguration-ImportPath) parameter in the *Amazon FSx API Reference*\.

Import occurs on cluster creation\. For more information, see [Importing data from your data repository](https://docs.aws.amazon.com/fsx/latest/LustreGuide/importing-files.html) in the *Amazon FSx for Lustre User Guide*\. On import, only file metadata \(name, ownership, timestamp, and permissions\) is imported\. File data isn't imported from the S3 bucket until the file is first accessed\. For details on preloading the file contents, see [Preloading files into your file system](https://docs.aws.amazon.com/fsx/latest/LustreGuide/preload-file-contents-hsm.html) in the *Amazon FSx for Lustre User Guide*\.

If a value isn't provided, the file system is empty\.

```
import_path =  s3://bucket
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `imported_file_chunk_size`<a name="fsx-imported-file-chunk-size"></a>

**\(Optional\)** Determines the stripe count and the maximum amount of data for each file \(in MiB\) stored on a single physical disk for files that are imported from a data repository \(using [`import_path`](#fsx-import-path)\)\. The maximum number of disks that a single file can be striped across is limited by the total number of disks that make up the file system\. When the [`imported_file_chunk_size`](#fsx-imported-file-chunk-size) parameter is specified, the [`automatic_backup_retention_days`](#fsx-automatic-backup-retention-days), [`copy_tags_to_backups`](#fsx-copy-tags-to-backups), [`daily_automatic_backup_start_time`](#fsx-daily-automatic-backup-start-time), and [`fsx_backup_id`](#fsx-backup-id) parameters must not be specified\. This corresponds to the [ImportedFileChunkSize](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-importedfilechunksize) property\.

The chunk size default is `1024` \(1 GiB\), and it can go as high as 512,000 MiB \(500 GiB\)\. Amazon S3 objects have a maximum size of 5 TB\.

```
imported_file_chunk_size = 1024
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `per_unit_storage_throughput`<a name="fsx-per-unit-storage-throughput"></a>

**\(Required for `PERSISTENT_1` deployment types\)** For the [`deployment_type`](#fsx-deployment-type)` = PERSISTENT_1` deployment type, describes the amount of read and write throughput for each 1 tebibyte \(TiB\) of storage, in MB/s/TiB\. File system throughput capacity is calculated by multiplying ﬁle system storage capacity \(TiB\) by the [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput) \(MB/s/TiB\)\. For a 2\.4 TiB ﬁle system, provisioning 50 MB/s/TiB of [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput) yields 120 MB/s of ﬁle system throughput\. You pay for the amount of throughput that you provision\. This corresponds to the [PerUnitStorageThroughput](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-perunitstoragethroughput) property\.

The possible values depend on the value of the [`storage_type`](#fsx-storage-type) setting\.

`storage\_type = SSD`  
The possible values are 50, 100, 200\.

`storage\_type = HDD`  
The possible values are 12, 40\.

```
per_unit_storage_throughput = 200
```

**Note**  
Support for [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput) was added in AWS ParallelCluster version 2\.6\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `shared_dir`<a name="fsx-shared-dir"></a>

**\(Required\)** Defines the mount point for the Amazon FSx for Lustre file system on the head and compute nodes\.

Don't use `NONE` or `/NONE` as the shared directory\.

The following example mounts the file system at `/fsx`\.

```
shared_dir = /fsx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `storage_capacity`<a name="fsx-storage-capacity"></a>

**\(Required\)** Specifies the storage capacity of the file system, in GiB\. This corresponds to the [StorageCapacity](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-fsx-filesystem.html#cfn-fsx-filesystem-storagecapacity) property\.

The storage capacity possible values vary based on the [`deployment_type`](#fsx-deployment-type) setting\.

`SCRATCH_1`  
The possible values are 1200, 2400, and any multiple of 3600\.

`SCRATCH_2`  
The possible values are 1200 and any multiple of 2400\.

`PERSISTENT_1`  
The possible values vary based on the values of other settings\.    
`storage\_type = SSD`  
The possible values are 1200 and any multiple of 2400\.  
`storage\_type = HDD`  
The possible values vary based on the setting of the [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput) setting\.    
`per\_unit\_storage\_throughput = 12`  
The possible values are any multiple of 6000\.  
`per\_unit\_storage\_throughput = 40`  
The possible values are any multiple of 1800\.

```
storage_capacity = 7200
```

**Note**  
For AWS ParallelCluster version 2\.5\.0 and 2\.5\.1, [`storage_capacity`](#fsx-storage-capacity) supported possible values of 1200, 2400, and any multiple of 3600\. For versions earlier than AWS ParallelCluster version 2\.5\.0, [`storage_capacity`](#fsx-storage-capacity) had a minimum size of 3600\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `storage_type`<a name="fsx-storage-type"></a>

**\(Optional\)** Specifies the storage type of the file system\. This corresponds to the [StorageType](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-fsx-filesystem.html#cfn-fsx-filesystem-storagetype) property\. The possible values are `SSD` and `HDD`\. The default is `SSD`\.

The storage type changes the possible values of other settings\.

`storage_type = SSD`   
Specifies a sold\-state drive \(SSD\) storage type\.  
`storage_type = SSD` changes the possible values of several other settings\.    
[`drive_cache_type`](#fsx-drive-cache-type)  
This setting cannot be specified\.  
[`deployment_type`](#fsx-deployment-type)  
This setting can be set to `SCRATCH_1`, `SCRATCH_2`, or `PERSISTENT_1`\.  
[`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput)  
This setting must be specified if [`deployment_type`](#fsx-deployment-type) is set to `PERSISTENT_1`\. The possible values are 50, 100, or 200\.  
[`storage_capacity`](#fsx-storage-capacity)  
This setting must be specified\. The possible values vary based on [`deployment_type`](#fsx-deployment-type)\.    
`deployment_type = SCRATCH_1`  
[`storage_capacity`](#fsx-storage-capacity) can be 1200, 2400, or any multiple of 3600\.  
`deployment_type = SCRATCH_2` or `deployment_type = PERSISTENT_1`  
[`storage_capacity`](#fsx-storage-capacity) can be 1200 or any multiple of 2400\.

`storage_type = HDD`  
Specifies a hard disk drive \(HDD\) storage type\.  
`storage_type = HDD` changes the possible values of other settings\.    
[`drive_cache_type`](#fsx-drive-cache-type)  
This setting can be specified\.  
[`deployment_type`](#fsx-deployment-type)  
This setting must be set to `PERSISTENT_1`\.  
[`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput)  
This setting must be specified\. The possible values are 12, or 40\.  
[`storage_capacity`](#fsx-storage-capacity)  
This setting must be specified\. The possible values vary based on the [`per_unit_storage_throughput`](#fsx-per-unit-storage-throughput) setting\.    
`storage_capacity = 12`  
[`storage_capacity`](#fsx-storage-capacity) can be any multiple of 6000\.  
`storage_capacity = 40`  
[`storage_capacity`](#fsx-storage-capacity) can be any multiple of 1800\.

```
storage_type = SSD
```

**Note**  
Support for the `storage\_type` setting was added in AWS ParallelCluster version 2\.10\.0\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `weekly_maintenance_start_time`<a name="fsx-weekly-maintenance-start-time"></a>

**\(Optional\)** Specifies a preferred time to perform weekly maintenance, in the UTC time zone\. This corresponds to the [WeeklyMaintenanceStartTime](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-fsx-filesystem-lustreconfiguration.html#cfn-fsx-filesystem-lustreconfiguration-weeklymaintenancestarttime) property\.

The format is \[day of week\]:\[hour of day\]:\[minute of hour\]\. For example, Monday at Midnight is:

```
weekly_maintenance_start_time = 1:00:00
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)