# Convert AWS ParallelCluster managed storage to external storage<a name="shared-storage-conversion-v3"></a>

Learn how to convert AWS ParallelCluster managed storage to external storage\.

The procedures are based on the following example configuration file snippet\.

```
...
  - MountDir: /fsx
    Name: fsx
    StorageType: FsxLustre
    FsxLustreSettings:
      StorageCapacity: 1200
      DeletionPolicy: Delete
...
```

**Convert AWS ParallelCluster managed storage to external storage**

1. Set the `DeletionPolicy` to `Retain` in the cluster configuration file\.

   ```
   ...
      - MountDir: /fsx
        Name: fsx
        StorageType: FsxLustre
        FsxLustreSettings:
          StorageCapacity: 1200
          DeletionPolicy: Retain
   ...
   ```

1. To set the `DeletionPolicy` change, run the following command\.

   ```
   pcluster update-cluster -n cluster-name -c cluster-config.yaml
   ```

1. Remove the `SharedStorage` section from the cluster configuration file\.

   ```
   ...
   ...
   ```

1. To change the managed `SharedStorage` to external `SharedStorage` and detach it from the cluster, run the following command\.

   ```
   pcluster update-cluster -n cluster-name -c cluster-config.yaml
   ```

1. Your shared storage is now external and detached from the cluster\.

1. To attach your external file system to the original cluster or another cluster, follow these steps\.

   1. Get the FSx for Lustre file system ID\.

      1. To use the AWS CLI run the following command and find the file system with a name that includes the name of your original cluster and note the file system ID\.

         ```
         aws fsx describe-file-systems
         ```

      1. To use the AWS Management Console, log in and navigate to the [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\. In the list of file systems, find the file system with a name that includes the name of your original cluster and note the file system ID\.

   1. Update the file system security group rules to provide access to and from the file system and cluster subnets\. You can find the file system security group name and ID in the Amazon FSx console\.

      Add rules to the file system security group that allow inbound and outbound TCP traffic from and to the head node and the compute node IP CIDR ranges or prefixes\. Specify TCP ports 988, 1021, 1022, and 1023 for the inbound and outbound TCP traffic\.

      For more information, see [`SharedStorage`](SharedStorage-v3.md) / [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId) and [Creating, configuring, and deleting security groups for Amazon EC2](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-sg.html) in the *AWS Command Line Interface User Guide for Version 2*\.

   1. Add the `SharedStorage` section to the cluster configuration\.

      ```
      ...
         - MountDir: /fsx
           Name: fsx-external
           StorageType: FsxLustre
           FsxLustreSettings:
             FileSystemId: fs-02e5b4b4abd62d51c
      ...
      ```

   1. To add the external shared storage to the cluster, run the following command\.

      ```
      pcluster update-cluster -n cluster-name -c cluster-config.yaml
      ```