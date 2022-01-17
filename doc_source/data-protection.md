# Data protection in AWS ParallelCluster<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AWS ParallelCluster\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form fields such as a **Name** field\. This includes when you work with AWS ParallelCluster or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.

## Data encryption<a name="security-data-encryption"></a>

A key feature of any secure service is that information is encrypted when it is not being actively used\.

### Encryption at rest<a name="security-data-encryption-at-rest"></a>

AWS ParallelCluster does not itself store any customer data other than the credentials it needs to interact with the AWS services on the user's behalf\.

For data on the nodes in the cluster, data can be encrypted at rest\.

For Amazon EBS volumes, encryption is configured using the [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings)/`Encrypted` and [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings)/`KmsKeyId` settings in the [`EbsSettings`](SharedStorage-v3.md#SharedStorage-v3-EbsSettings) section \([`encrypted`](ebs-section.md#encrypted) and [`ebs_kms_key_id`](ebs-section.md#ebs-kms-key-id) settings in the [`[ebs]` section](ebs-section.md) for AWS ParallelCluster version 2\.x\.\) For more information, see [Amazon EBS encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) in the Amazon EC2 User Guide for Linux Instances\.

For Amazon EFS volumes, encryption is configured using the [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings)/`Encrypted` and [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings)/`KmsKeyId` settings in the [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) \([`encrypted`](efs-section.md#efs-encrypted) and [`efs_kms_key_id`](efs-section.md#efs-efs-kms-key-id) settings in the [`[efs]` section](efs-section.md) in AWS ParallelCluster version 2\.x\)\. For more information, see [How encryption at rest works](https://docs.aws.amazon.com/efs/latest/ug/encryption-at-rest.html#howencrypt) in the *Amazon Elastic File System User Guide\. *

For FSx for Lustre file systems, encryption of data at rest is automatically enabled when creating an Amazon FSx file system\. For more information, see [Encrypting data at rest](https://docs.aws.amazon.com/fsx/latest/LustreGuide/encryption-at-rest.html) in the *Amazon FSx for Lustre User Guide*\.

For instance types with NVMe volumes, the data on NVMe instance store volumes is encrypted using an XTS\-AES\-256 cipher implemented on a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\. For more information, see [Encryption at rest](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html#encryption-rest) in the *Amazon EC2 User Guide for Linux Instances*\.

If you use AWS ParallelCluster to invoke an AWS service that transmits customer data to your local computer for storage, then refer to the Security and Compliance chapter in that service's User Guide for information on how that data is stored, protected, and encrypted\.

### Encryption in transit<a name="security-data-encryption-in-transit"></a>

By default, all data transmitted from the client computer running AWS ParallelCluster and AWS service endpoints is encrypted by sending everything through a HTTPS/TLS connection\. Traffic between the nodes in the cluster can be automatically encrypted, depending on the instance types selected\. For more information, see [Encryption in transit](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html#encryption-transit) in the *Amazon EC2 User Guide for Linux Instances*\.

## See also<a name="security-data-protection-seealso"></a>
+ [Data protection in Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html)
+ [Data protection in EC2 Image Builder](https://docs.aws.amazon.com/imagebuilder/latest/userguide/data-protection.html)
+ [Data protection in AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/security-data-protection.html)
+ [Data protection in Amazon EFS](https://docs.aws.amazon.com/efs/latest/ug/efs-backup-solutions.html)
+ [Data protection in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/DataDurability.html)
+ [Data protection in FSx for Lustre](https://docs.aws.amazon.com/fsx/latest/LustreGuide/data-protection.html)