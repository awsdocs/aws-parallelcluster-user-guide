# Shared storage<a name="shared-storage-quotas-integration-v3"></a>

AWS ParallelCluster supports [Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html), [FSx for ONTAP](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/what-is-fsx-ontap.html), and [FSx for OpenZFS](https://docs.aws.amazon.com/fsx/latest/OpenZFSGuide/what-is-fsx.html) shared storage volumes or [Amazon EFS](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html) and [FSx for Lustre](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html) shared storage file systems\.

In the [`SharedStorage` section](SharedStorage-v3.md), you can define either external or managed storage:
+ **External storage** refers to an existing volume or file system that's managed by the customer\. AWS ParallelCluster doesn't create or delete it\.
+ **Managed storage** refers to a volume or file system that's created by and can also be deleted by AWS ParallelCluster\.

You can configure AWS ParallelCluster to attach external storage to the cluster when the cluster is created or updated\. Similarly you can configure it to detach external storage from the cluster when the cluster is deleted or updated\. Your data is preserved and you can use it for long term permanent shared storage solutions that are independent of the cluster lifecycle\.

Managed storage is dependent on the lifecycle of the cluster by default\. You can remove the dependency with configuration settings as described in [Convert managed storage to external storage](#shared-storage-conversion-v3)\.

With specific settings, you can optimize each of the supported storage solutions for your use cases\.

For shared storage quotas, see [Quotas for shared storage](#shared-storage-quotas-v3)\.

To learn about shared storage and switching to new AWS ParallelCluster versions, see [Best practices: moving a cluster to a new AWS ParallelCluster minor or patch version](best-practices-v3.md#best-practices-cluster-upgrades-v3)\.

**Topics**
+ [Configure Amazon EBS, Amazon EFS, and FSx for Lustre shared storage](#shared-storage-config-ebs-efs-fsxlustre-v3)
+ [Shared storage considerations](#shared-storage-considerations-v3)
+ [Convert managed storage to external storage](#shared-storage-conversion-v3)
+ [Quotas for shared storage](#shared-storage-quotas-v3)

## Configure Amazon EBS, Amazon EFS, and FSx for Lustre shared storage<a name="shared-storage-config-ebs-efs-fsxlustre-v3"></a>

With Amazon EBS, Amazon EFS, and FSx for Lustre, you can specify either external or managed shared storage\.

To use an existing external Amazon EBS volume for long term permanent storage that's independent of the cluster life cycle, specify [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-VolumeId)\.

If you don't specify [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-VolumeId), AWS ParallelCluster creates a managed EBS volume from [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) when it creates the cluster and deletes the volume and data when the cluster is deleted by default\.

For a managed EBS volume, you can use the [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`DeletionPolicy`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-DeletionPolicy) to instruct AWS ParallelCluster to `Delete`, `Retain`, or `Snapshot` the volume when the cluster is deleted or the volume is removed from the cluster\.

To use an existing external Amazon EFS or FSx for Lustre file system for long term permanent storage that's independent of the cluster life cycle, specify [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-FileSystemId) or [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId)\.

If you don't specify [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-FileSystemId) or [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId), AWS ParallelCluster creates a managed Amazon EFS or FSx for Lustre file system from [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) or [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) when it creates the cluster\.

For a managed Amazon EFS or FSx for Lustre file system, you can use the [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) / [`DeletionPolicy`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-DeletionPolicy) or [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`DeletionPolicy`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-DeletionPolicy) to instruct AWS ParallelCluster to `Delete` or `Retain` the file system when either the file system is removed from the cluster or the cluster is deleted\.

### Configure FSx for ONTAP and FSx for OpenZFS shared storage<a name="shared-storage-config-ontap-zfs-v3"></a>

For FSx for ONTAP and FSx for OpenZFS, you can use [`FsxOntapSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxOntapSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-FsxOntapSettings-VolumeId) and [`FsxOpenZfsSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxOpenZfsSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-FsxOpenZfsSettings-VolumeId) to specify mounting an external existing volume for your cluster\. Managed shared storage isn't supported for FSx for ONTAP and FSx for OpenZFS\.

## Shared storage considerations<a name="shared-storage-considerations-v3"></a>

Consider the following when integrating shared storage with your cluster:
+ To add shared storage, you add a shared storage section to your configuration file and create or update the cluster\.
+ To remove shared storage, you remove the shared storage section from your configuration file and update the cluster\.
+ If you don't back up managed storage data and `DeletionPolicy` is `Delete`, your data is deleted when either your cluster is deleted or your managed storage is removed from the cluster during a cluster update\.
+ If you don't back up managed storage data and `DeletionPolicy` is `Retain`, your file system is detached before the cluster is deleted and can be re\-attached to another cluster as an external file system\. Your data is preserved\.
+ If managed storage is removed from the cluster configuration and `DeletionPolicy` is `Retain`, it can be re\-attached to the cluster as an external file system with your cluster data preserved\.

## Convert managed storage to external storage<a name="shared-storage-conversion-v3"></a>

**Update a cluster to change managed storage to external storage by taking the following steps:**

1. Set the `DeletionPolicy` to `Retain` in the cluster configuration file\.

1. Run `pcluster update-cluster -n cluster-name -c cluster-config.yaml` to set the `DeletionPolicy` change\.

1. Remove the `SharedStorage` section from the cluster configuration file\.

1. Run `pcluster update-cluster -n cluster-name -c cluster-config.yaml` to set the `SharedStorage` removal change\.

1. Your shared storage is now detached from the cluster and is equivalent to external storage that can be re\-attached to a cluster\.

## Quotas for shared storage<a name="shared-storage-quotas-v3"></a>

Configure cluster `SharedStorage` to mount existing shared file storage and create new shared file storage according to the quotas listed in the following table\.


**Mounted file storage quota per cluster**  

| File shared storage type | Managed storage | External storage | Quota net total | 
| --- | --- | --- | --- | 
|  Amazon EBS  |  5  |  5  |  5  | 
|  RAID  |  1  |  0  |  1  | 
|  Amazon EFS  |  1  |  20  |  21  | 
|  Amazon FSx †  |  1 FSx for Lustre  |  20  |  21  | 

**Note**  
Table added for AWS ParallelCluster version 3\.2\.0\.

† AWS ParallelCluster only supports mounting existing Amazon FSx for NetApp ONTAP and Amazon FSx for OpenZFS systems\. It doesn't support the creation of new FSx for ONTAP and FSx for OpenZFS systems\.

**Note**  
If AWS Batch is used as a scheduler, FSx for Lustre is only available on the cluster head node\.