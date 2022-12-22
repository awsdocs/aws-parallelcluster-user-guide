# `Iam` section<a name="Iam-v3"></a>

**\(Optional\)** Specifies IAM properties for the cluster\.

```
Iam:
  Roles:
    LambdaFunctionsRole: string
  PermissionsBoundary: string
  ResourcePrefix: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `Iam` properties<a name="Iam-v3.properties"></a>

`PermissionsBoundary` \(**Optional**, `String`\)  
The ARN of the IAM policy to use as permissions boundary for all roles created by AWS ParallelCluster\. For more information, see [Permissions boundaries for IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) in the *IAM User Guide*\. The format is `arn:${Partition}:iam::${Account}:policy/${PolicyName}`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`Roles` \(**Optional**\)  
Specifies settings for the IAM roles used by the cluster\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`LambdaFunctionsRole` \(**Optional**, `String`\)  
The ARN of the IAM role to use for AWS Lambda\. This overrides the default role attached to all Lambda functions backing AWS CloudFormation custom resources\. Lambda needs to be configured as the principal allowed to assume the role\. This will not override the role of Lambda functions used for AWS Batch\. The format is `arn:${Partition}:iam::${Account}:role/${RoleName}`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`ResourcePrefix` \(**Optional**\)  
Specifies a path or name prefix for IAM resources that are created by AWS ParallelCluster\.  
The resource prefix must follow the [naming rules specified by IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html):  
+ A name can contain up to 30 characters\.
+ A name can only be a string with no slash \(`/`\) characters\.
+ A path can be up to 512 characters\.
+ A path must start and end with a slash \(`/`\)\. It can contain multiple slashes \(`/`\) between the start and end slashes \(`/`\)\.
+ You can combine the path and name `/path/name`\.
Specify a name\.  

```
Iam:
  ResourcePrefix: my-prefix
```
Specify a path\.  

```
Iam:
  ResourcePrefix: /org/dept/team/project/user/
```
Specify a path and name\.  

```
Iam:
  ResourcePrefix: /org/dept/team/project/user/my-prefix
```
If you specify `/my-prefix`, an error is returned\.  

```
Iam:
  ResourcePrefix: /my-prefix
```
A configuration error is returned\. A path must have two `/`s\. A prefix by itself can't have `/`s\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)