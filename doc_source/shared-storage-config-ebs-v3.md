# Amazon Elastic Block Store<a name="shared-storage-config-ebs-v3"></a>

To use an existing external Amazon EBS volume for long term permanent storage that's independent of the cluster life cycle, specify [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-VolumeId)\.

To use an existing external Amazon EBS volume for long term permanent storage that's independent of the cluster life cycle, specify [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-VolumeId)\.

If you don't specify [`VolumeId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-VolumeId), by default, AWS ParallelCluster creates a managed EBS volume from [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) when your cluster is created\. AWS ParallelCluster also deletes the volume and data when the cluster is deleted or the volume is removed from the cluster configuration\.

For an AWS ParallelCluster managed EBS volume, you can use [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`DeletionPolicy`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-DeletionPolicy) to instruct AWS ParallelCluster to `Delete`, `Retain`, or `Snapshot` the volume when either the cluster is deleted or when the volume is removed from the cluster configuration\. By default, `DeletionPolicy` is set to `Delete`\.

**Warning**  
For AWS ParallelCluster managed shared storage, `DeletionPolicy` is set to `Delete` by default\.  
This means that, if one of the following is true, a managed volume and its data are deleted:  
You delete the cluster\.
You change the managed shared storage configuration [`SharedStorage`](SharedStorage-v3.md) / [`Name`](SharedStorage-v3.md#yaml-SharedStorage-Name)\.
You remove the managed shared storage from the configuration\.
We recommend that you back up your shared storage with snapshots regularly to avoid the loss of data\. For more information about Amazon EBS snapshots, see [Amazon EBS snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html) in the *Amazon Elastic Compute Cloud User Guide for Linux Instances*\. To learn how to manage data backups across AWS services, see [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) in the *AWS Backup Developer Guide*\.