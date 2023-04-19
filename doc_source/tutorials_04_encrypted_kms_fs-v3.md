# Configuring shared storage encryption with an AWS KMS key<a name="tutorials_04_encrypted_kms_fs-v3"></a>

Learn how to set up a customer managed AWS KMS key to encrypt and protect your data in the cluster file storage systems that are configured for AWS ParallelCluster\.

When using the AWS ParallelCluster command line interface \(CLI\) or API, you only pay for the AWS resources that are created when you create or update AWS ParallelCluster images and clusters\. For more information, see [AWS services used by AWS ParallelCluster](aws-services-v3.md)\.

The AWS ParallelCluster UI is built on a serverless architecture and you can use it within the AWS Free Tier category for most cases\. For more information, see [AWS ParallelCluster UI costs](install-pcui-v3.md#install-pcui-costs-v3)\.

AWS ParallelCluster supports following shared storage configuration options:
+ [`SharedStorage`](SharedStorage-v3.md) / [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) / [`KmsKeyId`](SharedStorage-v3.md#yaml-SharedStorage-EbsSettings-KmsKeyId)
+ [`SharedStorage`](SharedStorage-v3.md) / [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) / [`KmsKeyId`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-KmsKeyId)
+ [`SharedStorage`](SharedStorage-v3.md) / [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`KmsKeyId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-KmsKeyId)

You can use these options to provide a customer managed AWS KMS key for Amazon EBS, Amazon EFS, and FSx for Lustre shared storage system encryption\. To use them, you must create and configure an IAM policy for the following:
+ [`HeadNode`](HeadNode-v3.md) / [`Iam`](HeadNode-v3.md#HeadNode-v3-Iam) / [`AdditionalIamPolicies`](HeadNode-v3.md#yaml-HeadNode-Iam-AdditionalIamPolicies) / [`Policy`](HeadNode-v3.md#yaml-HeadNode-Iam-AdditionalIamPolicies-Policy)
+ [`Scheduler`](Scheduling-v3.md#yaml-Scheduling-Scheduler) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Iam`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Iam) / [`AdditionalIamPolicies`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Iam-AdditionalIamPolicies) / [`Policy`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Iam-AdditionalIamPolicies-Policy) 

**Prerequisites**
+ AWS ParallelCluster [is installed](install-v3-parallelcluster.md)\.
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
+ You have an [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ You have an IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) that are required to run the [`pcluster`](pcluster-v3.md) CLI\.

**Topics**
+ [Create the policy](#creating-the-role-v3)
+ [Configure and create the cluster](#creating-the-cluster-v3)

## Create the policy<a name="creating-the-role-v3"></a>

**Create a policy\.**

1. Go to the IAM Console: [https://console\.aws\.amazon\.com/iam/home](https://console.aws.amazon.com/iam/home)\.

1. Choose **Policies**\.

1. Choose **Create policy**\.

1. Choose the **JSON** tab and paste in the following policy\. Make sure to replace all occurrences of `123456789012` with your AWS account ID and the key Amazon Resource Name \(ARN\) and AWS Region with that of your own\.

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

1. Make a note of the policy ARN\. You need it to configure your cluster\.

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

Replace the items in red text with your own values\. Then, create a cluster that uses your AWS KMS key to encrypt your data in Amazon EBS\.

The configuration is similar for Amazon EFS and FSx for Lustre file systems\.

The Amazon EFS `SharedStorage` configuration is as follows\.

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

The FSx for Lustre `SharedStorage` configuration is as follows\.

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