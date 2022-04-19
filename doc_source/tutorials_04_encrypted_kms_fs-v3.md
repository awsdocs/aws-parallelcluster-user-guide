# Configuring shared storage encryption with a AWS KMS key<a name="tutorials_04_encrypted_kms_fs-v3"></a>

Learn how to set up a customer managed AWS KMS key to encrypt and protect your data in cluster file storage systems configured for AWS ParallelCluster\.

AWS ParallelCluster supports the configuration options:
+ `SharedStorage` / `EbsSettings` / `KmsKeyId`
+ `SharedStorage` / `EfsSettings` / `KmsKeyId`
+ `SharedStorage` / `FsxLustreSettings` / `KmsKeyId`

You can use these options to provide a customer managed AWS KMS key for Amazon EBS, Amazon EFS, and FSx for Lustre shared storage system encryption\. To use them, you must create and configure an IAM policy for:
+ `HeadNode` / `Iam` / `AdditionalIamPolicies` / `Policy`
+ `Scheduler` / `SlurmQueues` / `Iam` / `AdditionalIamPolicies` / `Policy` 

**Topics**
+ [Create the policy](#creating-the-role-v3)
+ [Configure and create the cluster](#creating-the-cluster-v3)

## Create the policy<a name="creating-the-role-v3"></a>

**Create a policy\.**

1. Go to the IAM Console: [https://console\.aws\.amazon\.com/iam/home](https://console.aws.amazon.com/iam/home)\.

1. Choose **Policies**\.

1. Choose **Create policy**\.

1. Choose the **JSON** tab and paste in the following policy\. Make sure to replace all occurrences of `123456789012` with your AWS account ID and the key Amazon Resource Name \(ARN\) and Region with that of your own\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "kms:DescribeKey",
                   "kms:ReEncrypt*",
                   "kms:CreateGrant",
                   "kms:Decrypt"
               ],
               "Resource": [
                   "arn:aws:kms:region-id:123456789012:key/abcd1234-ef56-gh78-ij90-abcd1234efgh5678"
               ]
           }
       ]
   }
   ```

1. For this tutorial, name the policy `ParallelClusterKmsPolicy`, and then choose **Create Policy**\.

1. Make a note of the policy ARN\. You'll need it to configure your cluster\.

## Configure and create the cluster<a name="creating-the-cluster-v3"></a>

The following is an example cluster configuration that includes an Amazon Elastic Block Store shared file system with encryption\.

```
Region: eu-west-1
Image:
  Os: alinux2
HeadNode:
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-abcdef01234567890
  Ssh:
    KeyName: my-ssh-key
  Iam:
    AdditionalIamPolicies:
      - Policy: arn:aws:iam::123456789012:policy/ParallelClusterKmsPolicy
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: q1
      ComputeResources:
        - Name: t2micro
          InstanceType: t2.micro
          MinCount: 0
          MaxCount: 10
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
      Iam:
        AdditionalIamPolicies:
          - Policy: arn:aws:iam::123456789012:policy/ParallelClusterKmsPolicy
SharedStorage:
  - MountDir: /shared/ebs1
    Name: shared-ebs1
    StorageType: Ebs
    EbsSettings:
      Encrypted: True
      KmsKeyId: abcd1234-ef56-gh78-ij90-abcd1234efgh5678
```

Replace the items in red text with your own values and create a cluster that uses your AWS KMS key to encrypt your data in Amazon EBS\.

The configuration is similar for Amazon EFS and FSx for Lustre file systems\.

Amazon EFS `SharedStorage` configuration:

```
...
SharedStorage:
  - MountDir: /shared/efs1
    Name: shared-efs1
    StorageType: Efs
    EfsSettings:
      Encrypted: True
      KmsKeyId: abcd1234-ef56-gh78-ij90-abcd1234efgh5678
```

FSx for Lustre `SharedStorage` configuration:

```
...
SharedStorage:
  - MountDir: /shared/fsx1
    Name: shared-fsx1
    StorageType: FsxLustre
    FsxLustreSettings:
      StorageCapacity: 1200
      DeploymentType: PERSISTENT_1
      PerUnitStorageThroughput: 200
      KmsKeyId: abcd1234-ef56-gh78-ij90-abcd1234efgh5678
```