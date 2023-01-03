# Amazon FSx for Lustre<a name="shared-storage-config-fsxlustre-v3"></a>

To use an existing external FSx for Lustre file system for long\-term permanent storage outside of the cluster life cycle, specify [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId)\.

If you don't specify [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId), by default, AWS ParallelCluster creates a managed FSx for Lustre file system from [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) when it creates the cluster\. AWS ParallelCluster also deletes the file system and data when the cluster is deleted or when the file system is removed from the cluster configuration\.

For an AWS ParallelCluster managed FSx for Lustre file system, you can use the [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`DeletionPolicy`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-DeletionPolicy) to instruct AWS ParallelCluster to `Delete` or `Retain` the file system when either the cluster is deleted or when the file system is removed from the cluster configuration\. By default, `DeletionPolicy` is set to `Delete`\.

**Warning**  
For AWS ParallelCluster managed shared storage, `DeletionPolicy` is set to `Delete` by default\.  
This means that, if one of the following is true, a managed file system and its data are deleted:  
You delete the cluster\.
You change the managed shared storage configuration [`SharedStorage`](SharedStorage-v3.md) / [`Name`](SharedStorage-v3.md#yaml-SharedStorage-Name)\.
You remove the managed shared storage from the configuration\.
We recommend that you back up your shared storage regularly to avoid the loss of data\. You can define backups in your cluster with [`SharedStorage`](SharedStorage-v3.md) / [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`AutomaticBackupRetentionDays`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-AutomaticBackupRetentionDays) and [`DailyAutomaticBackupStartTime`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-DailyAutomaticBackupStartTime)\. To learn how to manage data backups across AWS services, see [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) in the *AWS Backup Developer Guide*\.