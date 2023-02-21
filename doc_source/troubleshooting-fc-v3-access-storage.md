# Trying to access storage<a name="troubleshooting-fc-v3-access-storage"></a>

## Using an external Amazon FSx for Lustre file system<a name="access-storage-fsx-lustre-v3"></a>

Make sure that traffic is allowed between the cluster and file system\. The file system must be associated with a security group that allows inbound and outbound TCP traffic through ports 988, 1021, 1022, and 1023\. For more information about how to set up security groups, see [FileSystemId](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId)\.

## Using an external Amazon Elastic File System file system<a name="access-storage-efs-v3"></a>

Make sure that traffic is allowed between the cluster and file system\. The file system must be associated with a security group that allows inbound and outbound TCP traffic through ports 988, 1021, 1022, and 1023\. For more information about how to set up security groups, see [FileSystemId](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-FileSystemId)\.