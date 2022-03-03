# `Iam` section<a name="Iam-v3"></a>

**\(Optional\)** Specifies IAM properties for the cluster\.

```
Iam:
  Roles:
    LambdaFunctionsRole: string
  PermissionsBoundary: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `Iam` Properties<a name="Iam-v3.properties"></a>

`PermissionsBoundary` \(**Optional**, `String`\)  
The ARN of the IAM policy to use as permissions boundary for all roles created by AWS ParallelCluster\. For more information, see [Permissions boundaries for IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) in the *IAM User Guide*\. The format is `arn:${Partition}:iam::${Account}:policy/${PolicyName}`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`Roles` \(**Optional**\)  
Specifies settings for the IAM roles used by the cluster\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`LambdaFunctionsRole` \(**Optional**, `String`\)  
The ARN of the IAM role to use for AWS Lambda\. This overrides the default role attached to all Lambda functions backing AWS CloudFormation custom resources\. Lambda needs to be configured as the principal allowed to assume the role\. This will not override the role of Lambda functions used for AWS Batch\. The format is `arn:${Partition}:iam::${Account}:role/${RoleName}`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)