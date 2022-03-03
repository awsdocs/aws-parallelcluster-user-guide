# `Build` section<a name="Build-v3"></a>

**\(Required\)** Specifies the configuration in which the image will be built\.

```
Build:
  InstanceType: string
  SubnetId: string
  ParentImage: string
  Iam:
    InstanceRole: string
    InstanceProfile: string
    CleanupLambdaRole: string
    AdditionalIamPolicies:
      - Policy: string
    PermissionsBoundary: string
  Components:
    - Type: string
      Value: string
  Tags:
    - Key: string
      Value: string
  SecurityGroupIds:
    - string
  UpdateOsPackages:
    Enabled: boolean
```

## `Build` Properties<a name="Build-v3.properties"></a>

`InstanceType` \(**Required**, `String`\)  
Specifies the instance type for the instance used to build the image\.

`SubnetId` \(**Optional**, `String`\)  
Specifies the ID of an existing subnet in which to provision the instance to build the image\. The provided subnet requires internet access\.  
`pcluster build-image` uses the default VPC\. If the default VPC has been deleted, perhaps by using AWS Control Tower or AWS Landing Zone, then the subnet ID must be specified\.

`ParentImage` \(**Required**, `String`\)  
Specifies the base image\. The parent image can be either a non AWS ParallelCluster AMI or an official AWS ParallelCluster AMI for the same version\. You can't use a AWS ParallelCluster official or custom AMI from a different version of AWS ParallelCluster\. The format must either be the ARN of a image `arn:Partition:imagebuilder:Region:Account:image/ImageName/ImageVersion` or an AMI ID `ami-12345678`\.

`SecurityGroupIds` \(**Optional**, `[String]`\)  
Specifies the list of security group IDs for the image\.

### `Iam`<a name="Build-v3-Iam"></a>

#### `Iam` Properties<a name="Build-v3-Iam.properties"></a>

\(**Optional**\) Specifies the IAM resources for the image build\.

```
Iam:
  InstanceRole: string
  InstanceProfile: string
  CleanupLambdaRole: string
  AdditionalIamPolicies:
    - Policy: string
  PermissionsBoundary: string
```

`InstanceProfile` \(**Optional**, `String`\)  
Specifies an instance profile to override the default instance profile for the EC2 Image Builder instance\. `InstanceProfile` and `InstanceRole` and `AdditionalIamPolicies` cannot be specified together\. The format is `arn:Partition:iam::Account:instance-profile/InstanceProfileName`\.

`InstanceRole` \(**Optional**, `String`\)  
Specifies an instance role to override the default instance role for the EC2 Image Builder instance\. `InstanceProfile` and `InstanceRole` and `AdditionalIamPolicies` cannot be specified together\. The format is `arn:Partition:iam::Account:role/RoleName`\.

`CleanupLambdaRole` \(**Optional**, `String`\)  
The ARN of the IAM role to use for the AWS Lambda function backing the AWS CloudFormation custom resource that removes build artifacts on build completion\. Lambda needs to be configured as the principal allowed to assume the role\. The format is `arn:Partition:iam::Account:role/RoleName`\.

`AdditionalIamPolicies` \(**Optional**\)  
Specifies additional IAM policies to attach to the EC2 Image Builder instance used to produce the custom AMI\.  

```
AdditionalIamPolicies:
  - Policy: string
```  
`Policy` \(**Optional**, `[String]`\)  
List of IAM policies\. The format is `arn:Partition:iam::Account:policy/PolicyName`\.

`PermissionsBoundary` \(**Optional**, `String`\)  
The ARN of the IAM policy to use as permissions boundary for all roles created by AWS ParallelCluster\. For more information on IAM permissions boundaries please refer to [Permissions boundaries for IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) in the *IAM User Guide*\. The format is `arn:Partition:iam::Account:policy/PolicyName`\.

### `Components`<a name="Build-v3-Components"></a>

#### `Components` Properties<a name="Build-v3-Components.properties"></a>

\(**Optional**\) Specifies EC2 ImageBuilder components to use during the AMI build process in addition to the ones provided by default by AWS ParallelCluster\. Such components can be used to customize the AMI build process\.

```
Components:
  - Type: string
    Value: string
```

`Type` \(**Optional**, `String`\)  
Specifies the type of the type\-value pair for the component\. Type can be `arn` or `script`\.

`Value` \(**Optional**, `String`\)  
Specifies the value of the type\-value pair for the component\. When type is `arn`, this is the ARN of a EC2 Image Builder component\. When type is `script`, this is the https or s3 link pointing to the script to use when creating the EC2 Image Builder component\.

### `Tags`<a name="Build-v3-Tags"></a>

#### `Tags` Properties<a name="Build-v3-Tags.properties"></a>

\(**Optional**\) Specifies the list of tags to be set in the resources used to build the AMI\.

```
Tags:
  - Key: string
    Value: string
```

`Key` \(**Optional**, `String`\)  
Defines the name of the tag\.

`Value` \(**Optional**, `String`\)  
Defines the value of the tag\.

### `UpdateOsPackages`<a name="Build-v3-UpdateOsPackages"></a>

#### `UpdateOsPackages` Properties<a name="Build-v3-UpdateOsPackages.properties"></a>

\(**Optional**\) Specifies whether the operating system is updated before installing AWS ParallelCluster software stack\.

```
UpdateOsPackages:
  Enabled: boolean
```

`Enabled` \(**Optional**, `Boolean`\)  
If `true`, the OS is updated and rebooted before installing the AWS ParallelCluster software\. The default is `false`\.