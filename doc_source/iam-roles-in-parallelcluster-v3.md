# AWS Identity and Access Management roles in AWS ParallelCluster 3\.x<a name="iam-roles-in-parallelcluster-v3"></a>

AWS ParallelCluster uses AWS Identity and Access Management \(IAM\) roles to control permissions that are associated with the AWS resources deployed to the AWS account\. In AWS ParallelCluster we can identify two types of IAM roles: the one that is assumed by the user that invokes the CLI commands and the ones that are associated with AWS ParallelCluster resources, such as the EC2 instances launched in a cluster\.

By default, AWS ParallelCluster takes care of creating all needed IAM roles that are configured with the minimal set of policies required by AWS ParallelCluster resources\. However, the user that invokes the various AWS ParallelCluster operations must have the right level of permissions to create or modify all of the necessary resources\. 

**Topics**
+ [Using existing IAM roles with AWS ParallelCluster](#iam-roles-in-parallelcluster-v3-existing-roles)
+ [AWS ParallelCluster example user policies](#iam-roles-in-parallelcluster-v3-example-user-policies)
+ [AWS ParallelCluster parameters to control IAM permissions](#iam-roles-in-parallelcluster-v3-params-for-iam)

## Using existing IAM roles with AWS ParallelCluster<a name="iam-roles-in-parallelcluster-v3-existing-roles"></a>

You can use existing IAM roles when creating a cluster or building a custom EC2 image\. Typically, you choose existing IAM roles to fully control the permissions that are granted to AWS ParallelCluster resources and to the users of the cluster\. The following examples show the IAM policies and roles that are required to both invoke AWS ParallelCluster features and customize permissions associated with cluster EC2 instances\.

In the policies, replace `<REGION>`, `<AWS ACCOUNT ID>`, and similar strings with the appropriate values\.

## AWS ParallelCluster example user policies<a name="iam-roles-in-parallelcluster-v3-example-user-policies"></a>

The AWS ParallelCluster user role refers to the IAM role assumed by the user of the AWS ParallelCluster CLI\.

The following example policies include Amazon Resource Names \(ARNs\) for the resources\. If you're working in the AWS GovCloud \(US\) or AWS China partitions, the ARNs must be changed\. Specifically, they must be changed from "arn:aws" to "arn:aws\-us\-gov" for the AWS GovCloud \(US\) partition or "arn:aws\-cn" for the AWS China partition\. For more information, see [Amazon Resource Names \(ARNs\) in AWS GovCloud \(US\) Regions](https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/using-govcloud-arns.html) in the *AWS GovCloud \(US\) User Guide* and [ARNs for AWS services in China](https://docs.amazonaws.cn/aws/latest/userguide/ARNs.html) in *Getting Started with AWS services in China*\.

**Topics**
+ [Base user policy required to invoke AWS ParallelCluster features](#iam-roles-in-parallelcluster-v3-base-user-policy)
+ [Additional user policy when using AWS Batch scheduler](#iam-roles-in-parallelcluster-v3-user-policy-batch)
+ [User Policy to use AWS ParallelCluster image build features](#iam-roles-in-parallelcluster-v3-user-policy-build-image)
+ [User Policy to manage IAM resources](#iam-roles-in-parallelcluster-v3-user-policy-manage-iam)

### Base user policy required to invoke AWS ParallelCluster features<a name="iam-roles-in-parallelcluster-v3-base-user-policy"></a>

The following policy shows the permissions required to execute AWS ParallelCluster commands\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ec2:Describe*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "EC2Read"
        },
        {
            "Action": [
                "ec2:AllocateAddress",
                "ec2:AssociateAddress",
                "ec2:AttachNetworkInterface",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:CreateLaunchTemplate",
                "ec2:CreateLaunchTemplateVersion",
                "ec2:CreateNetworkInterface",
                "ec2:CreatePlacementGroup",
                "ec2:CreateSecurityGroup",
                "ec2:CreateSnapshot",
                "ec2:CreateTags",
                "ec2:CreateVolume",
                "ec2:DeleteLaunchTemplate",
                "ec2:DeleteNetworkInterface",
                "ec2:DeletePlacementGroup",
                "ec2:DeleteSecurityGroup",
                "ec2:DeleteVolume",
                "ec2:DisassociateAddress",
                "ec2:ModifyLaunchTemplate",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:ModifyVolume",
                "ec2:ModifyVolumeAttribute",
                "ec2:ReleaseAddress",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:RunInstances",
                "ec2:TerminateInstances"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "EC2Write"
        },
        {
            "Action": [
                "dynamodb:DescribeTable",
                "dynamodb:ListTagsOfResource",
                "dynamodb:CreateTable",
                "dynamodb:DeleteTable",
                "dynamodb:GetItem",
                "dynamodb:PutItem",
                "dynamodb:Query",
                "dynamodb:TagResource"
            ],
            "Resource": "arn:aws:dynamodb:*:<AWS ACCOUNT ID>:table/parallelcluster-*",
            "Effect": "Allow",
            "Sid": "DynamoDB"
        },
        {
            "Action": [
                "route53:ChangeResourceRecordSets",
                "route53:ChangeTagsForResource",
                "route53:CreateHostedZone",
                "route53:DeleteHostedZone",
                "route53:GetChange",
                "route53:GetHostedZone",
                "route53:ListResourceRecordSets",
                "route53:ListQueryLoggingConfigs"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "Route53HostedZones"
        },
        {
            "Action": [
                "cloudformation:*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "CloudFormation"
        },
        {
            "Action": [
                "cloudwatch:PutDashboard",
                "cloudwatch:ListDashboards",
                "cloudwatch:DeleteDashboards",
                "cloudwatch:GetDashboard"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "CloudWatch"
        },
        {
            "Action": [
                "iam:GetRole",
                "iam:GetRolePolicy",
                "iam:GetPolicy",
                "iam:SimulatePrincipalPolicy",
                "iam:GetInstanceProfile"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/*",
                "arn:aws:iam::<AWS ACCOUNT ID>:policy/*",
                "arn:aws:iam::aws:policy/*",
                "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/*"
            ],
            "Effect": "Allow",
            "Sid": "IamRead"
        },
        {
            "Action": [
                "iam:CreateInstanceProfile",
                "iam:DeleteInstanceProfile",
                "iam:AddRoleToInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamInstanceProfile"
        },
        {
            "Condition": {
                "StringEqualsIfExists": {
                    "iam:PassedToService": [
                        "lambda.amazonaws.com",
                        "ec2.amazonaws.com",
                        "spotfleet.amazonaws.com"
                    ]
                }
            },
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamPassRole"
        },
        {
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "fsx.amazonaws.com",
                        "s3.data-source.lustre.fsx.amazonaws.com"
                    ]
                }
            },
            "Action": [
                "iam:CreateServiceLinkedRole",
                "iam:DeleteServiceLinkedRole"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "lambda:CreateFunction",
                "lambda:DeleteFunction",
                "lambda:GetFunctionConfiguration",
                "lambda:GetFunction",
                "lambda:InvokeFunction",
                "lambda:AddPermission",
                "lambda:RemovePermission",
                "lambda:UpdateFunctionConfiguration"
            ],
            "Resource": [
                "arn:aws:lambda:*:<AWS ACCOUNT ID>:function:parallelcluster-*",
                "arn:aws:lambda:*:<AWS ACCOUNT ID>:function:pcluster-*"
            ],
            "Effect": "Allow",
            "Sid": "Lambda"
        },
        {
            "Action": [
                "s3:*"
            ],
            "Resource": [
                "arn:aws:s3:::parallelcluster-*",
                "arn:aws:s3:::aws-parallelcluster-*"
            ],
            "Effect": "Allow",
            "Sid": "S3ResourcesBucket"
        },
        {
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": "arn:aws:s3:::*-aws-parallelcluster*",
            "Effect": "Allow",
            "Sid": "S3ParallelClusterReadOnly"
        },
        {
            "Action": [
                "fsx:*"
            ],
            "Resource": [
                "arn:aws:fsx:*:<AWS ACCOUNT ID>:*"
            ],
            "Effect": "Allow",
            "Sid": "FSx"
        },
        {
            "Action": [
                "elasticfilesystem:*"
            ],
            "Resource": [
                "arn:aws:elasticfilesystem:*:<AWS ACCOUNT ID>:*"
            ],
            "Effect": "Allow",
            "Sid": "EFS"
        },
        {
            "Action": [
                "logs:DeleteLogGroup",
                "logs:PutRetentionPolicy",
                "logs:DescribeLogGroups",
                "logs:CreateLogGroup",
                "logs:FilterLogEvents",
                "logs:GetLogEvents",
                "logs:CreateExportTask",
                "logs:DescribeLogStreams",
                "logs:DescribeExportTasks"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "CloudWatchLogs"
        }
    ]
}
```

### Additional user policy when using AWS Batch scheduler<a name="iam-roles-in-parallelcluster-v3-user-policy-batch"></a>

In case you need to create and manage a cluster with AWS Batch scheduler the following additional policy is required\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Condition": {
                "StringEqualsIfExists": {
                    "iam:PassedToService": [
                        "ecs-tasks.amazonaws.com",
                        "batch.amazonaws.com",
                        "codebuild.amazonaws.com"
                    ]
                }
            },
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamPassRole"
        },
        {
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "batch.amazonaws.com"
                    ]
                }
            },
            "Action": [
                "iam:CreateServiceLinkedRole",
                "iam:DeleteServiceLinkedRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/aws-service-role/batch.amazonaws.com/*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "codebuild:*"
            ],
            "Resource": "arn:aws:codebuild:*:<AWS ACCOUNT ID>:project/pcluster-*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "ecr:*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "ECR"
        },
        {
            "Action": [
                "batch:*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "Batch"
        },
        {
            "Action": [
                "events:*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "AmazonCloudWatchEvents"
        },
        {
            "Action": [
                "ecs:DescribeContainerInstances",
                "ecs:ListContainerInstances"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "ECS"
        }
    ]
}
```

### User Policy to use AWS ParallelCluster image build features<a name="iam-roles-in-parallelcluster-v3-user-policy-build-image"></a>

Users that intend to create custom EC2 images with AWS ParallelCluster will need to have the following set of permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ec2:DescribeImages",
                "ec2:DescribeInstanceTypeOfferings",
                "ec2:DescribeInstanceTypes",
                "ec2:DeregisterImage",
                "ec2:DeleteSnapshot"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "EC2"
        },
        {
            "Action": [
                "iam:CreateInstanceProfile",
                "iam:AddRoleToInstanceProfile",
                "iam:GetRole",
                "iam:GetRolePolicy",
                "iam:GetInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/parallelcluster/*",
                "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/ParallelClusterImage*",
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IAM"
        },
        {
            "Condition": {
                "StringEquals": {
                    "iam:PassedToService": [
                        "lambda.amazonaws.com",
                        "ec2.amazonaws.com"
                    ]
                }
            },
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/parallelcluster/*",
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IAMPassRole"
        },
        {
            "Action": [
                "logs:CreateLogGroup",
                "logs:DeleteLogGroup"
            ],
            "Resource": [
                "arn:aws:logs:*:<AWS ACCOUNT ID>:log-group:/aws/imagebuilder/ParallelClusterImage-*",
                "arn:aws:logs:*:<AWS ACCOUNT ID>:log-group:/aws/lambda/ParallelClusterImage-*"
            ],
            "Effect": "Allow",
            "Sid": "CloudWatch"
        },
        {
            "Action": [
                "cloudformation:DescribeStacks",
                "cloudformation:CreateStack",
                "cloudformation:DeleteStack"
            ],
            "Resource": [
                "arn:aws:cloudformation:*:<AWS ACCOUNT ID>:stack/*"
            ],
            "Effect": "Allow",
            "Sid": "CloudFormation"
        },
        {
            "Action": [
                "lambda:CreateFunction",
                "lambda:GetFunction",
                "lambda:AddPermission",
                "lambda:RemovePermission",
                "lambda:DeleteFunction"
            ],
            "Resource": [
                "arn:aws:lambda:*:<AWS ACCOUNT ID>:function:ParallelClusterImage-*"
            ],
            "Effect": "Allow",
            "Sid": "Lambda"
        },
        {
            "Action": [
                "imagebuilder:Get*"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "ImageBuilderGet"
        },
        {
            "Action": [
                "imagebuilder:CreateImage",
                "imagebuilder:TagResource",
                "imagebuilder:CreateImageRecipe",
                "imagebuilder:CreateComponent",
                "imagebuilder:CreateDistributionConfiguration",
                "imagebuilder:CreateInfrastructureConfiguration",
                "imagebuilder:DeleteImage",
                "imagebuilder:DeleteComponent",
                "imagebuilder:DeleteImageRecipe",
                "imagebuilder:DeleteInfrastructureConfiguration",
                "imagebuilder:DeleteDistributionConfiguration"
            ],
            "Resource": [
                "arn:aws:imagebuilder:*:<AWS ACCOUNT ID>:image/parallelclusterimage-*",
                "arn:aws:imagebuilder:*:<AWS ACCOUNT ID>:image-recipe/parallelclusterimage-*",
                "arn:aws:imagebuilder:*:<AWS ACCOUNT ID>:component/parallelclusterimage-*",
                "arn:aws:imagebuilder:*:<AWS ACCOUNT ID>:distribution-configuration/parallelclusterimage-*",
                "arn:aws:imagebuilder:*:<AWS ACCOUNT ID>:infrastructure-configuration/parallelclusterimage-*"
            ],
            "Effect": "Allow",
            "Sid": "ImageBuilder"
        },
        {
            "Action": [
                "s3:CreateBucket",
                "s3:ListBucket",
                "s3:ListBucketVersions"
            ],
            "Resource": [
                "arn:aws:s3:::parallelcluster-*"
            ],
            "Effect": "Allow",
            "Sid": "S3Bucket"
        },
        {
            "Action": [
                "sns:GetTopicAttributes",
                "sns:TagResource",
                "sns:CreateTopic",
                "sns:Subscribe",
                "sns:Publish",
                "SNS:DeleteTopic",
                "SNS:Unsubscribe"
            ],
            "Resource": [
                "arn:aws:sns:*:<AWS ACCOUNT ID>:ParallelClusterImage-*"
            ],
            "Effect": "Allow",
            "Sid": "SNS"
        },
        {
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:DeleteObject",
                "s3:DeleteObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::parallelcluster-*/*"
            ],
            "Effect": "Allow",
            "Sid": "S3Objects"
        },
        {
            "Action": "iam:CreateServiceLinkedRole",
            "Effect": "Allow",
            "Resource": "arn:aws:iam::*:role/aws-service-role/imagebuilder.amazonaws.com/AWSServiceRoleForImageBuilder",
            "Condition": {
                "StringLike": {
                    "iam:AWSServiceName": "imagebuilder.amazonaws.com"
                }
            }
        }
    ]
}
```

### User Policy to manage IAM resources<a name="iam-roles-in-parallelcluster-v3-user-policy-manage-iam"></a>

When using AWS ParallelCluster to create clusters or custom AMIs, IAM policies and roles need to be provided in order to grant the required set of permissions to the various AWS ParallelCluster components\. Such IAM resources can be either automatically created by AWS ParallelCluster or need to be provided as input when creating cluster or custom image resources\.

Based on the additional IAM policies granted to the AWS ParallelCluster user we can enable the following use cases:

**Topics**
+ [Privileged IAM access mode](#iam-roles-in-parallelcluster-v3-privileged-iam-access)
+ [Restricted IAM access mode](#iam-roles-in-parallelcluster-v3-restricted-iam-access)
+ [`PermissionsBoundary` mode](#iam-roles-in-parallelcluster-v3-permissionsboundary-mode)

#### Privileged IAM access mode<a name="iam-roles-in-parallelcluster-v3-privileged-iam-access"></a>

With this mode AWS ParallelCluster takes care of automating the creation of all necessary IAM resources\. The advantage of delegating the creation of IAM resources to AWS ParallelCluster consists in the fact that IAM policies are scoped down to enable access to cluster resources only\.

To enable such mode add the following policy to the AWS ParallelCluster user role\.

**Warning**  
This mode enables the user to have IAM admin privileges in the AWS account

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "iam:CreateServiceLinkedRole",
                "iam:DeleteRole",
                "iam:TagRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamRole"
        },
        {
            "Action": [
                "iam:CreateRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamCreateRole"
        },
        {
            "Action": [
                "iam:PutRolePolicy",
                "iam:DeleteRolePolicy"
            ],
            "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
            "Effect": "Allow",
            "Sid": "IamInlinePolicy"
        },
        {
            "Condition": {
                "ArnLike": {
                    "iam:PolicyARN": [
                        "arn:aws:iam::<AWS ACCOUNT ID>:policy/parallelcluster*",
                        "arn:aws:iam::<AWS ACCOUNT ID>:policy/parallelcluster/*",
                        "arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy",
                        "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore",
                        "arn:aws:iam::aws:policy/AWSBatchFullAccess",
                        "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess",
                        "arn:aws:iam::aws:policy/service-role/AWSBatchServiceRole",
                        "arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceforEC2Role",
                        "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy",
                        "arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetTaggingRole",
                        "arn:aws:iam::aws:policy/EC2InstanceProfileForImageBuilder",
                        "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
                    ]
                }
            },
            "Action": [
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy"
            ],
            "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
            "Effect": "Allow",
            "Sid": "IamPolicy"
        }
    ]
}
```

#### Restricted IAM access mode<a name="iam-roles-in-parallelcluster-v3-restricted-iam-access"></a>

When no additional IAM policies are granted to the AWS ParallelCluster user, IAM roles required by clusters or custom image build need to be manually created by an IAM administrator and passed as part of the resource configuration\.

When creating a cluster the following parameters are required:
+  `Iam` / `Roles` / `LambdaFunctionsRole`
+  `HeadNode` / `Iam` / `InstanceRole` \| `InstanceProfile`
+  `Scheduling` / ``SlurmQueues`` / ``Iam`` / `InstanceRole` \| `InstanceProfile`

When building a custom image the following parameters are required:
+ `Build` / ``Iam`` / `InstanceRole` \| `InstanceProfile` 
+ `Build` / ``Iam`` / `CleanupLambdaRole`

The IAM roles passed as part of the above listed parameters have to be created on the `/parallelcluster/` path prefix\. If this is not possible the AWS ParallelCluster user policy needs to be update to grant `iam:PassRole` permission on the specific custom roles, like in the example below\.

```
{
            "Condition": {
                "StringEqualsIfExists": {
                    "iam:PassedToService": [
                        "ecs-tasks.amazonaws.com",
                        "lambda.amazonaws.com",
                        "ec2.amazonaws.com",
                        "spotfleet.amazonaws.com",
                        "batch.amazonaws.com",
                        "codebuild.amazonaws.com"
                    ]
                }
            },
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                <list all custom IAM roles>
            ],
            "Effect": "Allow",
            "Sid": "IamPassRole"
}
```

**Warning**  
Currently this mode does not allow the management of AWS Batch clusters because not all IAM roles can be passed in the cluster configuration\.

#### `PermissionsBoundary` mode<a name="iam-roles-in-parallelcluster-v3-permissionsboundary-mode"></a>

This mode delegates to AWS ParallelCluster the creation of IAM roles, however such roles are bound to the configured IAM permissions boundary\. For more information on IAM permissions boundaries please refer to [Permissions boundaries for IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) in the *IAM User Guide*\.

The following policy needs to be added to the AWS ParallelCluster user role\.

In the policy, replace *<permissions\-boundary\-arn>* with the IAM policy ARN to be enforced as permissions boundary\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "iam:CreateServiceLinkedRole",
                "iam:DeleteRole",
                "iam:TagRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamRole"
        },
        {
            "Condition": {
                "StringEquals": {
                    "iam:PermissionsBoundary": [
                        <permissions-boundary-arn>
                    ]
                }
            },
            "Action": [
                "iam:CreateRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "IamCreateRole"
        },
        {
            "Condition": {
                "StringEquals": {
                    "iam:PermissionsBoundary": [
                        <permissions-boundary-arn>
                    ]
                }
            },
            "Action": [
                "iam:PutRolePolicy",
                "iam:DeleteRolePolicy"
            ],
            "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
            "Effect": "Allow",
            "Sid": "IamInlinePolicy"
        },
        {
            "Condition": {
                "StringEquals": {
                    "iam:PermissionsBoundary": [
                        <permissions-boundary-arn>
                    ]
                },
                "ArnLike": {
                    "iam:PolicyARN": [
                        "arn:aws:iam::<AWS ACCOUNT ID>:policy/parallelcluster*",
                        "arn:aws:iam::<AWS ACCOUNT ID>:policy/parallelcluster/*",
                        "arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy",
                        "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore",
                        "arn:aws:iam::aws:policy/AWSBatchFullAccess",
                        "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess",
                        "arn:aws:iam::aws:policy/service-role/AWSBatchServiceRole",
                        "arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceforEC2Role",
                        "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy",
                        "arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetTaggingRole",
                        "arn:aws:iam::aws:policy/EC2InstanceProfileForImageBuilder",
                        "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
                    ]
                }
            },
            "Action": [
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy"
            ],
            "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
            "Effect": "Allow",
            "Sid": "IamPolicy"
        }
    ]
}
```

where `<permissions-boundary-arn>` is the IAM policy ARN to be enforced as permissions boundary\.

When this mode is enabled the permissions boundary ARN has to be specified when creating/updating a cluster through the `Iam` / `PermissionsBoundary` config parameter and when building a custom image through the `Build / Iam / PermissionBoundary` parameter\.

## AWS ParallelCluster parameters to control IAM permissions<a name="iam-roles-in-parallelcluster-v3-params-for-iam"></a>

AWS ParallelCluster exposes a series of configuration options to control and customize the IAM roles that are used in a cluster or during the custom AMI creation process\. 

**Topics**
+ [Cluster configuration](#iam-roles-in-parallelcluster-v3-cluster-config)
+ [Custom Image configuration](#iam-roles-in-parallelcluster-v3-custom-image-configuration)

### Cluster configuration<a name="iam-roles-in-parallelcluster-v3-cluster-config"></a>

**Topics**
+ [Head node IAM role](#iam-roles-in-parallelcluster-v3-cluster-config-headnode-instanceprofile)
+ [Amazon S3 access](#iam-roles-in-parallelcluster-v3-cluster-config-headnode-s3access)
+ [Additional IAM policies](#iam-roles-in-parallelcluster-v3-cluster-config-additionaliampolicies)
+ [AWS Lambda functions role](#iam-roles-in-parallelcluster-v3-cluster-config-lambdafunctionsrole)
+ [Compute nodes IAM role](#iam-roles-in-parallelcluster-v3-cluster-config-slurmqueues-instanceprofile)
+ [Permissions boundary](#iam-roles-in-parallelcluster-v3-cluster-config-permissionsboundary)

#### Head node IAM role<a name="iam-roles-in-parallelcluster-v3-cluster-config-headnode-instanceprofile"></a>

`HeadNode` / `Iam` / `InstanceRole` \| `InstanceProfile`

This option allows to override the IAM role that is assigned to the head node of the cluster\. For additional details, please refer to the `InstanceProfile` reference\.

Here is the minimal set of policies to be used as part of this role when the scheduler is Slurm:
+ `arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy` managed IAM policy\. For more information, see [Create IAM roles and users for use with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent.html) in the *Amazon CloudWatch User Guide*\.
+ `arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore` managed IAM policy\. For more information, see [AWS managed policies for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/security_iam_service-with-iam.html#managed-policies) in the *AWS Systems Manager User Guide*\.
+ Additional IAM policy:

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Action": [
                  "s3:GetObject",
                  "s3:GetObjectVersion"
              ],
              "Resource": [
                  "arn:aws:s3:::<REGION>-aws-parallelcluster/*",
                  "arn:aws:s3:::dcv-license.<REGION>/*",
                  "arn:aws:s3:::parallelcluster-*-v1-do-not-delete/*"
              ],
              "Effect": "Allow"
          },
          {
              "Action": [
                  "dynamodb:GetItem",
                  "dynamodb:PutItem",
                  "dynamodb:BatchWriteItem"
              ],
              "Resource": "arn:aws:dynamodb:<REGION>:<AWS ACCOUNT ID>:table/parallelcluster-*",
              "Effect": "Allow"
          },
          {
              "Condition": {
                  "StringEquals": {
                      "ec2:ResourceTag/parallelcluster:node-type": "Compute"
                  }
              },
              "Action": "ec2:TerminateInstances",
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Action": "ec2:RunInstances",
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Condition": {
                  "StringEquals": {
                      "iam:PassedToService": [
                          "ec2.amazonaws.com"
                      ]
                  }
              },
              "Action": [
                  "iam:PassRole"
              ],
              "Resource": [
                  "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
                  "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/parallelcluster/*"
              ],
              "Effect": "Allow"
          },
          {
              "Action": [
                  "ec2:DescribeInstances",
                  "ec2:DescribeInstanceStatus",
                  "ec2:CreateTags",
                  "ec2:DescribeVolumes",
                  "ec2:AttachVolume",
                  "ec2:DescribeInstanceAttribute"
              ],
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "cloudformation:DescribeStackResource",
                  "cloudformation:SignalResource"
              ],
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "route53:ChangeResourceRecordSets"
              ],
              "Resource": "*",
              "Effect": "Allow"
          }
      ]
  }
  ```

Note that in case `Scheduling / SlurmQueues / Iam / InstanceRole` is used to override the compute IAM role, the head node policy reported above needs to include such role in the `Resource` section of the `iam:PassRole` permission\.

Here is the minimal set of policies to be used as part of this role when the scheduler is AWS Batch:
+ `arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy` managed IAM policy\. For more information, see [Create IAM roles and users for use with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent.html) in the *Amazon CloudWatch User Guide*\.
+ `arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore` managed IAM policy\. For more information, see [AWS managed policies for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/security_iam_service-with-iam.html#managed-policies) in the *AWS Systems Manager User Guide*\.
+  `arn:aws:iam::aws:policy/AWSBatchFullAccess` managed IAM policy\. For more information, see [AWS managed policy: `BatchFullAccess`](https://docs.aws.amazon.com/batch/latest/userguide/security-iam-awsmanpol.html#security-iam-awsmanpol-BatchFullAccess) in the *AWS Batch User Guide*\.
+ Additional IAM policy:

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Action": [
                  "s3:GetObject",
                  "s3:PutObject",
                  "s3:GetObjectVersion"
              ],
              "Resource": [
                  "arn:aws:s3:::parallelcluster-*-v1-do-not-delete/*"
              ],
              "Effect": "Allow"
          },
          {
              "Action": "s3:GetObject",
              "Resource": [
                  "arn:aws:s3:::dcv-license.<REGION>/*",
                  "arn:aws:s3:::<REGION>-aws-parallelcluster/*"
              ],
              "Effect": "Allow"
          },
          {
              "Condition": {
                  "StringEquals": {
                      "iam:PassedToService": [
                          "batch.amazonaws.com"
                      ]
                  }
              },
              "Action": [
                  "iam:PassRole"
              ],
              "Resource": [
                  "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
                  "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/parallelcluster/*"
              ],
              "Effect": "Allow"
          },
          {
              "Action": [
                  "ec2:DescribeInstances",
                  "ec2:DescribeInstanceStatus",
                  "ec2:CreateTags",
                  "ec2:DescribeVolumes",
                  "ec2:AttachVolume",
                  "ec2:DescribeInstanceAttribute"
              ],
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "cloudformation:DescribeStackResource",
                  "cloudformation:DescribeStacks",
                  "cloudformation:SignalResource"
              ],
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "route53:ChangeResourceRecordSets"
              ],
              "Resource": "*",
              "Effect": "Allow"
          }
      ]
  }
  ```

#### Amazon S3 access<a name="iam-roles-in-parallelcluster-v3-cluster-config-headnode-s3access"></a>

`HeadNode` / `Iam` / `S3Access` or `Scheduling` / `SlurmQueues` / `Iam` / `S3Access`

This configuration sections allow to customize the Amazon S3 access by granting additional Amazon S3 policies to the IAM roles associated with the head node or compute nodes of the cluster when such roles are created by AWS ParallelCluster\. For more information, see the reference documentation for each of the configuration parameter\.

This parameter can be only used when the AWS ParallelCluster user is configured in [Privileged IAM access mode](#iam-roles-in-parallelcluster-v3-privileged-iam-access) or [`PermissionsBoundary` mode](#iam-roles-in-parallelcluster-v3-permissionsboundary-mode)\.

#### Additional IAM policies<a name="iam-roles-in-parallelcluster-v3-cluster-config-additionaliampolicies"></a>

`HeadNode` / `Iam` / `AdditionalIamPolicies` or `Scheduling` / `SlurmQueues` / `Iam` / `AdditionalIamPolicies`

This configuration sections allow to attach additional managed IAM policies to the IAM roles associated with the head node or compute nodes of the cluster when such roles are created by AWS ParallelCluster\.

To use this option make sure the AWS ParallelCluster user is granted `iam:AttachRolePolicy` and `iam:DetachRolePolicy` permissions for the IAM policies that need to be attached\.

#### AWS Lambda functions role<a name="iam-roles-in-parallelcluster-v3-cluster-config-lambdafunctionsrole"></a>

`Iam` / [Roles](Iam-v3.md#yaml-Iam-Roles) / `LambdaFunctionsRole`

This option overrides the role attached to all AWS Lambda functions that are used during the cluster creation process\. AWS Lambda needs to be configured as the principal allowed to assume the role\.

Here is the minimal set of policies to be used as part of this role:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "route53:ListResourceRecordSets",
        "route53:ChangeResourceRecordSets"
      ],
      "Resource": "arn:aws:route53:::hostedzone/*",
      "Effect": "Allow"
    },
    {
      "Action": ["logs:CreateLogStream", "logs:PutLogEvents"],
      "Effect": "Allow",
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Action": "ec2:DescribeInstances",
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Action": "ec2:TerminateInstances",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/parallelcluster:node-type": "Compute"
        }
      },
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Action": [
        "s3:DeleteObject",
        "s3:DeleteObjectVersion",
        "s3:ListBucket",
        "s3:ListBucketVersions"
      ],
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::parallelcluster-*-v1-do-not-delete",
        "arn:aws:s3:::parallelcluster-*-v1-do-not-delete/*"
      ]
    }
  ]
}
```

#### Compute nodes IAM role<a name="iam-roles-in-parallelcluster-v3-cluster-config-slurmqueues-instanceprofile"></a>

`Scheduling` / `SlurmQueues` / `Iam` / `InstanceRole` \| `InstanceProfile`

This option allows to override the IAM role that is assigned to the compute nodes of the cluster\. For more information, see `InstanceProfile`\.

Here is the minimal set of policies to be used as part of this role:
+ `arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy` managed IAM policy\. For more information, see [Create IAM roles and users for use with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent.html) in the *Amazon CloudWatch User Guide*\.
+ `arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore` managed IAM policy\. For more information, see [AWS managed policies for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/security_iam_service-with-iam.html#managed-policies) in the *AWS Systems Manager User Guide*\.
+ Additional IAM policy:

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Action": [
                  "dynamodb:Query"
              ],
              "Resource": "arn:aws:dynamodb:<REGION>:<AWS ACCOUNT ID>:table/parallelcluster-*",
              "Effect": "Allow"
          },
          {
              "Action": "s3:GetObject",
              "Resource": [
                  "arn:aws:s3:::<REGION>-aws-parallelcluster/*"
              ],
              "Effect": "Allow"
          },
          {
              "Action": "ec2:DescribeInstanceAttribute",
              "Resource": "*",
              "Effect": "Allow"
          }
      ]
  }
  ```

#### Permissions boundary<a name="iam-roles-in-parallelcluster-v3-cluster-config-permissionsboundary"></a>

`Iam` / `PermissionsBoundary`

This parameter forces AWS ParallelCluster to attach the given IAM policy as a `PermissionsBoundary` to all IAM roles that are created as part of a cluster deployment\.

See [`PermissionsBoundary` mode](#iam-roles-in-parallelcluster-v3-permissionsboundary-mode) for the list of policies required by the AWS ParallelCluster user to use such functionality\.

### Custom Image configuration<a name="iam-roles-in-parallelcluster-v3-custom-image-configuration"></a>

**Topics**
+ [Instance role for EC2 Image Builder](#iam-roles-in-parallelcluster-v3-custom-image-configuration-instancerole)
+ [AWS Lambda cleanup role](#iam-roles-in-parallelcluster-v3-custom-image-configuration-cleanuplambdarole)
+ [Additional IAM policies](#iam-roles-in-parallelcluster-v3-custom-image-configuration-additionaliampolicies)
+ [Permissions boundary](#iam-roles-in-parallelcluster-v3-custom-image-configuration-permissionsboundary)

#### Instance role for EC2 Image Builder<a name="iam-roles-in-parallelcluster-v3-custom-image-configuration-instancerole"></a>

`Build` / `Iam` / `InstanceRole` \| `InstanceProfile`

This option allows to override the IAM role that is assigned to the EC2 instance launched by EC2 Image Builder to create a custom AMI\.

Here is the minimal set of policies to be used as part of this role:
+ `arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore` managed IAM policy\. For more information, see [AWS managed policies for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/security_iam_service-with-iam.html#managed-policies) in the *AWS Systems Manager User Guide*\.
+ `arn:aws:iam::aws:policy/EC2InstanceProfileForImageBuilder` managed IAM policy\. For more information, see [`EC2InstanceProfileForImageBuilder` policy](https://docs.aws.amazon.com/imagebuilder/latest/userguide/security-iam-awsmanpol.html#sec-iam-manpol-EC2InstanceProfileForImageBuilder) in the *Image Builder User Guide*\.
+ Additional IAM policy:

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Action": [
                  "ec2:CreateTags",
                  "ec2:ModifyImageAttribute"
              ],
              "Resource": "arn:aws:ec2:<REGION>::image/*",
              "Effect": "Allow"
          }
      ]
  }
  ```

#### AWS Lambda cleanup role<a name="iam-roles-in-parallelcluster-v3-custom-image-configuration-cleanuplambdarole"></a>

`Build` / `Iam` / `CleanupLambdaRole`

This option overrides the role attached to all AWS Lambda functions that are used during the custom image build process\. AWS Lambda needs to be configured as the principal allowed to assume the role\.

Here is the minimal set of policies to be used as part of this role:
+ `arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole` managed IAM policy\. For more information, see [AWS managed policies for Lambda features](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html#permissions-executionrole-features) in the *AWS Lambda Developer Guide*\.
+ Additional IAM policy:

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Action": [
                  "iam:DetachRolePolicy",
                  "iam:DeleteRole",
                  "iam:DeleteRolePolicy"
              ],
              "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster/*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "iam:DeleteInstanceProfile",
                  "iam:RemoveRoleFromInstanceProfile"
              ],
              "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/parallelcluster/*",
              "Effect": "Allow"
          },
          {
              "Action": "imagebuilder:DeleteInfrastructureConfiguration",
              "Resource": "arn:aws:imagebuilder:<REGION>:<AWS ACCOUNT ID>:infrastructure-configuration/parallelclusterimage-*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "imagebuilder:DeleteComponent"
              ],
              "Resource": [
                  "arn:aws:imagebuilder:<REGION>:<AWS ACCOUNT ID>:component/parallelclusterimage-*/*"
              ],
              "Effect": "Allow"
          },
          {
              "Action": "imagebuilder:DeleteImageRecipe",
              "Resource": "arn:aws:imagebuilder:<REGION>:<AWS ACCOUNT ID>:image-recipe/parallelclusterimage-*/*",
              "Effect": "Allow"
          },
          {
              "Action": "imagebuilder:DeleteDistributionConfiguration",
              "Resource": "arn:aws:imagebuilder:<REGION>:<AWS ACCOUNT ID>:distribution-configuration/parallelclusterimage-*",
              "Effect": "Allow"
          },
          {
              "Action": "imagebuilder:DeleteImage",
              "Resource": "arn:aws:imagebuilder:<REGION>:<AWS ACCOUNT ID>:image/parallelclusterimage-*/*",
              "Effect": "Allow"
          },
          {
              "Action": "cloudformation:DeleteStack",
              "Resource": "arn:aws:cloudformation:<REGION>:<AWS ACCOUNT ID>:stack/*/*",
              "Effect": "Allow"
          },
          {
              "Action": "ec2:CreateTags",
              "Resource": "arn:aws:ec2:<REGION>::image/*",
              "Effect": "Allow"
          },
          {
              "Action": "tag:TagResources",
              "Resource": "*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "lambda:DeleteFunction",
                  "lambda:RemovePermission"
              ],
              "Resource": "arn:aws:lambda:<REGION>:<AWS ACCOUNT ID>:function:ParallelClusterImage-*",
              "Effect": "Allow"
          },
          {
              "Action": "logs:DeleteLogGroup",
              "Resource": "arn:aws:logs:<REGION>:<AWS ACCOUNT ID>:log-group:/aws/lambda/ParallelClusterImage-*:*",
              "Effect": "Allow"
          },
          {
              "Action": [
                  "SNS:GetTopicAttributes",
                  "SNS:DeleteTopic",
                  "SNS:GetSubscriptionAttributes",
                  "SNS:Unsubscribe"
              ],
              "Resource": "arn:aws:sns:<REGION>:<AWS ACCOUNT ID>:ParallelClusterImage-*",
              "Effect": "Allow"
          }
      ]
  }
  ```

#### Additional IAM policies<a name="iam-roles-in-parallelcluster-v3-custom-image-configuration-additionaliampolicies"></a>

`Build` / `Iam` / `AdditionalIamPolicies`

This configuration section allows to attach additional managed IAM policies to the role associated with the EC2 instance used by EC2 Image Builder to produce the custom AMI\.

To use this option make sure the AWS ParallelCluster user is granted `iam:AttachRolePolicy` and `iam:DetachRolePolicy` permissions for the IAM policies that need to be attached\.

#### Permissions boundary<a name="iam-roles-in-parallelcluster-v3-custom-image-configuration-permissionsboundary"></a>

`Build` / `Iam` / `PermissionsBoundary`

This parameter forces AWS ParallelCluster to attach the given IAM policy as a `PermissionsBoundary` to all IAM roles that are created as part of custom AMI build\.

See [`PermissionsBoundary` mode](#iam-roles-in-parallelcluster-v3-permissionsboundary-mode) for the list of policies required to use such functionality\.