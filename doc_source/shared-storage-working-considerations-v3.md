# AWS ParallelCluster shared storage considerations<a name="shared-storage-working-considerations-v3"></a>

Consider the following when working with shared storage in AWS ParallelCluster\.
+ Back up your file system data with [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) or another method to manage backups for all of your storage systems\.
+ To add shared storage, you add a shared storage section to your configuration file and create or update the cluster\.
+ To remove shared storage, you remove the shared storage section from your configuration file and update the cluster\.
+ To replace existing AWS ParallelCluster managed shared storage with new managed storage, change the value for [`SharedStorage`](SharedStorage-v3.md) / [`Name`](SharedStorage-v3.md#yaml-SharedStorage-Name) and update the cluster\.
**Warning**  
By default, the existing AWS ParallelCluster managed storage and data is deleted when you perform the cluster update with a new `Name` parameter\. If you need to change `Name` and retain the existing managed shared storage data, make sure you either set the `DeletionPolicy` to `Retain` or back up the data before you update the cluster\.
+ If you don't back up AWS ParallelCluster managed storage data and `DeletionPolicy` is `Delete`, your data is deleted when either your cluster is deleted or when your managed storage is removed from the cluster configuration and the cluster is updated\.
+ If you don't back up AWS ParallelCluster managed storage data and `DeletionPolicy` is `Retain`, your file system is detached before the cluster is deleted and can be re\-attached to another cluster as an external file system\. Your data is preserved\.
+ If AWS ParallelCluster managed storage is removed from the cluster configuration and `DeletionPolicy` is `Retain`, it can be re\-attached to the cluster as an external file system with your cluster data preserved\.
+ Starting with AWS ParallelCluster version 3\.4\.0, you can enhance security for Amazon EFS file system mounts by configuring [`SharedStorage`](SharedStorage-v3.md) / [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) / [`EncryptionInTransit`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-EncryptionInTransit) and [`IamAuthorization`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-IamAuthorization) settings\.