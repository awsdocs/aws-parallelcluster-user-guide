# `Image` section<a name="Image-v3"></a>

**\(Required\)** Defines the operating system for the cluster\.

```
Image:
  Os: string
  CustomAmi: string
```

## `Image` Properties<a name="Image-v3.properties"></a>

`Os` \(**Required**, `String`\)  
Specifies the operating system to use for the cluster\. The supported values are `alinux2`, `centos7`, `ubuntu1804`, and `ubuntu2004`\.  
Other than the specific Regions mentioned in the following table that don't support `centos7`\. All other AWS commercial Regions support all of the following operating systems\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/Image-v3.html)
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CustomAmi` \(**Optional**, `String`\)  
Specifies the ID of a custom AMI to use for the head and compute nodes instead of the default AMI\. For more information, see [AWS ParallelCluster AMI customization](custom-ami-v3.md)\.  
If the custom AMI requires additional permissions for its launch, these permissions must be added to both the user and head node policies\.  
For example, if a custom AMI has an encrypted snapshot associated with it, the following additional policies are required in both the user and head node policies:  

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
               "arn:aws:kms:<AWS_REGION>:<AWS_ACCOUNT_ID>:key/<AWS_KMS_KEY_ID>"
           ]                                                    
       }
   ]
}
```
To troubleshoot custom AMI validation warnings, see [Troubleshooting custom AMI issues](troubleshooting-v3.md#troubleshooting-v3-custom-amis)\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)