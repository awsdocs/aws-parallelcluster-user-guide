# AWS Identity and Access Management Roles in AWS ParallelCluster<a name="iam"></a>

AWS ParallelCluster uses AWS Identity and Access Management \(IAM\) roles for Amazon EC2 to enable instances to access AWS services for the deployment and operation of a cluster\. By default, the IAM role for Amazon EC2 is created as part of the cluster creation by AWS CloudFormation\. This means that the user that creates the cluster must have the appropriate level of permissions, as described in the following sections\.

AWS ParallelCluster uses multiple AWS services to deploy and operate a cluster\. See the complete list in the [AWS Services used in AWS ParallelCluster](aws-services.md) section\.

## Defaults<a name="defaults"></a>

When you use the default settings for cluster creation, an IAM role for Amazon EC2 is created by the cluster\. The user that is creating the cluster must have the right level of permissions to create all of the resources required to launch the cluster, including an IAM role for Amazon EC2\. Typically, the IAM user must have the permissions of an *AdministratorAccess* managed policy\. For details about managed policies, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

## Using an Existing IAM Role for Amazon EC2<a name="using-an-existing-ec2-iam-role"></a>

 You can use an existing IAM role for Amazon EC2 when creating a cluster, but you must first define the IAM policy and role before attempting to launch the cluster\. Typically, you choose an existing IAM role for Amazon EC2 to reduce the permissions that are granted to users as they launch clusters\. The following example shows an IAM policy and role for both Amazon EC2 and the AWS ParallelCluster\. You must create both as individual policies in IAM and then attach to the appropriate resources\. In both policies, replace *<REGION>*, *<AWS ACCOUNT ID>*, and similar strings with the appropriate values\.

## ParallelClusterInstancePolicy<a name="parallelclusterinstancepolicy"></a>

The following example sets the `ParallelClusterInstancePolicy`, using SGE, Slurm, or Torque as the scheduler:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ec2:DescribeVolumes",
                "ec2:AttachVolume",
                "ec2:DescribeInstanceAttribute",
                "ec2:DescribeInstanceStatus",
                "ec2:DescribeInstances",
                "ec2:DescribeRegions"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow",
            "Sid": "EC2"
        },
        {
            "Action": [
                "dynamodb:ListTables"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow",
            "Sid": "DynamoDBList"
        },
        {
            "Action": [
                "sqs:SendMessage",
                "sqs:ReceiveMessage",
                "sqs:ChangeMessageVisibility",
                "sqs:DeleteMessage",
                "sqs:GetQueueUrl"
            ],
            "Resource": [
                "arn:aws:sqs:<REGION>:<AWS ACCOUNT ID>:parallelcluster-*"
            ],
            "Effect": "Allow",
            "Sid": "SQSQueue"
        },
        {
            "Action": [
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:TerminateInstanceInAutoScalingGroup",
                "autoscaling:SetDesiredCapacity",
                "autoScaling:UpdateAutoScalingGroup",
                "autoscaling:DescribeTags",
                "autoScaling:SetInstanceHealth"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow",
            "Sid": "Autoscaling"
        },
        {
            "Action": [
                "cloudformation:DescribeStacks",
                "cloudformation:DescribeStackResource"
            ],
            "Resource": [
                "arn:aws:cloudformation:<REGION>:<AWS ACCOUNT ID>:stack/parallelcluster-*/*"
            ],
            "Effect": "Allow",
            "Sid": "CloudFormation"
        },
        {
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:Query",
                "dynamodb:GetItem",
                "dynamodb:DeleteItem",
                "dynamodb:DescribeTable"
            ],
            "Resource": [
                "arn:aws:dynamodb:<REGION>:<AWS ACCOUNT ID>:table/parallelcluster-*"
            ],
            "Effect": "Allow",
            "Sid": "DynamoDBTable"
        },
        {
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<REGION>-aws-parallelcluster/*"
            ],
            "Effect": "Allow",
            "Sid": "S3GetObj"
        },
        {
            "Resource": [
                "*"
            ],
            "Action": [
                "sqs:ListQueues"
            ],
            "Effect": "Allow",
            "Sid": "SQSList"
        },
        {
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster-*"
            ],
            "Effect": "Allow",
            "Sid": "BatchJobPassRole"
        },
        {
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::dcv-license.<REGION>/*"
            ],
            "Effect": "Allow",
            "Sid": "DcvLicense"
        }
    ]
}
```

The following example sets the `ParallelClusterInstancePolicy`, using `awsbatch` as the scheduler\. You must include the same policies that are assigned to the `BatchUserRole` that is defined in the AWS Batch AWS CloudFormation nested stack\. The `BatchUserRole` ARN is provided as a stack output\. Here is an overview of the required permissions:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "batch:SubmitJob",
                "batch:RegisterJobDefinition",
                "cloudformation:DescribeStacks",
                "ecs:ListContainerInstances",
                "ecs:DescribeContainerInstances",
                "logs:GetLogEvents",
                "logs:FilterLogEvents",
                "s3:PutObject",
                "s3:Get*",
                "s3:DeleteObject",
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:batch:<REGION>:<AWS ACCOUNT ID>:job-definition/<AWS_BATCH_STACK - JOB_DEFINITION_SERIAL_NAME>:1",
                "arn:aws:batch:<REGION>:<AWS ACCOUNT ID>:job-definition/<AWS_BATCH_STACK - JOB_DEFINITION_MNP_NAME>*",
                "arn:aws:batch:<REGION>:<AWS ACCOUNT ID>:job-queue/<AWS_BATCH_STACK - JOB_QUEUE_NAME>",
                "arn:aws:cloudformation:<REGION>:<AWS ACCOUNT ID>:stack/<STACK NAME>/*",
                "arn:aws:s3:::<RESOURCES S3 BUCKET>/batch/*",
                "arn:aws:iam::<AWS ACCOUNT ID>:role/<AWS_BATCH_STACK - JOB_ROLE>",
                "arn:aws:ecs:<REGION>:<AWS ACCOUNT ID>:cluster/<ECS COMPUTE ENVIRONMENT>",
                "arn:aws:ecs:<REGION>:<AWS ACCOUNT ID>:container-instance/*",
                "arn:aws:logs:<REGION>:<AWS ACCOUNT ID>:log-group:/aws/batch/job:log-stream:*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "s3:List*"
            ],
            "Resource": [
                "arn:aws:s3:::<RESOURCES S3 BUCKET>"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "batch:DescribeJobQueues",
                "batch:TerminateJob",
                "batch:DescribeJobs",
                "batch:CancelJob",
                "batch:DescribeJobDefinitions",
                "batch:ListJobs",
                "batch:DescribeComputeEnvironments",
                "ec2:DescribeInstances"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```

## ParallelClusterUserPolicy<a name="parallelclusteruserpolicy"></a>

The following example sets the `ParallelClusterUserPolicy`, using SGE, Slurm, or Torque as the scheduler\.

**Note**  
If you use a custom role, `[`ec2_iam_role`](cluster-definition.md#ec2-iam-role) = <role_name>`, you must change the IAM resource to include the name of that role from:  
`"Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster-*"`  
To:  
`"Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/<role_name>"`

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EC2Describe",
            "Action": [
                "ec2:DescribeKeyPairs",
                "ec2:DescribeRegions",
                "ec2:DescribeVpcs",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribePlacementGroups",
                "ec2:DescribeImages",
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceStatus",
                "ec2:DescribeSnapshots",
                "ec2:DescribeVolumes",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeAddresses",
                "ec2:CreateTags",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeAvailabilityZones"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "NetworkingEasyConfig",
            "Action": [
                "ec2:CreateVpc",
                "ec2:ModifyVpcAttribute",
                "ec2:DescribeNatGateways",
                "ec2:CreateNatGateway",
                "ec2:DescribeInternetGateways",
                "ec2:CreateInternetGateway",
                "ec2:AttachInternetGateway",
                "ec2:DescribeRouteTables",
                "ec2:CreateRouteTable",
                "ec2:AssociateRouteTable",
                "ec2:CreateSubnet",
                "ec2:ModifySubnetAttribute"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "EC2Modify",
            "Action": [
                "ec2:CreateVolume",
                "ec2:RunInstances",
                "ec2:AllocateAddress",
                "ec2:AssociateAddress",
                "ec2:AttachNetworkInterface",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:CreateNetworkInterface",
                "ec2:CreateSecurityGroup",
                "ec2:ModifyVolumeAttribute",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:DeleteNetworkInterface",
                "ec2:DeleteVolume",
                "ec2:TerminateInstances",
                "ec2:DeleteSecurityGroup",
                "ec2:DisassociateAddress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:ReleaseAddress",
                "ec2:CreatePlacementGroup",
                "ec2:DeletePlacementGroup"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "AutoScalingDescribe",
            "Action": [
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeAutoScalingInstances"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "AutoScalingModify",
            "Action": [
                "autoscaling:CreateAutoScalingGroup",
                "ec2:CreateLaunchTemplate",
                "ec2:ModifyLaunchTemplate",
                "ec2:DeleteLaunchTemplate",
                "ec2:DescribeLaunchTemplates",
                "ec2:DescribeLaunchTemplateVersions",
                "autoscaling:PutNotificationConfiguration",
                "autoscaling:UpdateAutoScalingGroup",
                "autoscaling:PutScalingPolicy",
                "autoscaling:DescribeScalingActivities",
                "autoscaling:DeleteAutoScalingGroup",
                "autoscaling:DeletePolicy",
                "autoscaling:DisableMetricsCollection",
                "autoscaling:EnableMetricsCollection"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "DynamoDBDescribe",
            "Action": [
                "dynamodb:DescribeTable"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "DynamoDBModify",
            "Action": [
              "dynamodb:CreateTable",
              "dynamodb:DeleteTable",
              "dynamodb:TagResource"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "SQSDescribe",
            "Action": [
                "sqs:GetQueueAttributes"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "SQSModify",
            "Action": [
                "sqs:CreateQueue",
                "sqs:SetQueueAttributes",
                "sqs:DeleteQueue",
                "sqs:TagQueue"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "SNSDescribe",
            "Action": [
              "sns:ListTopics",
              "sns:GetTopicAttributes"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "SNSModify",
            "Action": [
                "sns:CreateTopic",
                "sns:Subscribe",
                "sns:DeleteTopic"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "CloudFormationDescribe",
            "Action": [
                "cloudformation:DescribeStackEvents",
                "cloudformation:DescribeStackResource",
                "cloudformation:DescribeStackResources",
                "cloudformation:DescribeStacks",
                "cloudformation:ListStacks",
                "cloudformation:GetTemplate"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "CloudFormationModify",
            "Action": [
                "cloudformation:CreateStack",
                "cloudformation:DeleteStack",
                "cloudformation:UpdateStack"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "S3ParallelClusterReadOnly",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::<REGION>-aws-parallelcluster*"
            ]
        },
        {
            "Sid": "IAMModify",
            "Action": [
                "iam:PassRole",
                "iam:CreateRole",
                "iam:CreateServiceLinkedRole",
                "iam:DeleteRole",
                "iam:GetRole",
                "iam:TagRole",
                "iam:SimulatePrincipalPolicy"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:iam::<AWS ACCOUNT ID>:role/<PARALLELCLUSTER EC2 ROLE NAME>",
                "arn:aws:iam::<AWS ACCOUNT ID>:role/aws-service-role/*"
            ]
        },
        {
            "Sid": "IAMCreateInstanceProfile",
            "Action": [
                "iam:CreateInstanceProfile",
                "iam:DeleteInstanceProfile"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/*"
        },
        {
            "Sid": "IAMInstanceProfile",
            "Action": [
                "iam:AddRoleToInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile",
                "iam:GetRolePolicy",
                "iam:GetPolicy",
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy",
                "iam:PutRolePolicy",
                "iam:DeleteRolePolicy"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "EFSDescribe",
            "Action": [
                "elasticfilesystem:DescribeMountTargets",
                "elasticfilesystem:DescribeMountTargetSecurityGroups",
                "ec2:DescribeNetworkInterfaceAttribute"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "SSMDescribe",
            "Action": [
                "ssm:GetParametersByPath"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "FSx",
            "Effect": "Allow",
            "Action": [
                "fsx:*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "EFS",
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "CloudWatchLogs",
            "Effect": "Allow",
            "Action": [
                "logs:DeleteLogGroup",
                "logs:PutRetentionPolicy",
                "logs:DescribeLogGroups",
                "logs:CreateLogGroup"
            ],
            "Resource": "*"
        }
    ]
}
```

The following example sets the ParallelClusterUserPolicy, using `awsbatch` as the scheduler:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EC2Describe",
      "Action": [
        "ec2:DescribeKeyPairs",
        "ec2:DescribeRegions",
        "ec2:DescribeVpcs",
        "ec2:DescribeSubnets",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribePlacementGroups",
        "ec2:DescribeImages",
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceStatus",
        "ec2:DescribeSnapshots",
        "ec2:DescribeVolumes",
        "ec2:DescribeVpcAttribute",
        "ec2:DescribeAddresses",
        "ec2:CreateTags",
        "ec2:DescribeNetworkInterfaces",
        "ec2:DescribeAvailabilityZones"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
       "Sid": "EC2LaunchTemplate",
       "Action": [
           "ec2:CreateLaunchTemplate",
           "ec2:ModifyLaunchTemplate",
           "ec2:DeleteLaunchTemplate",
           "ec2:DescribeLaunchTemplates",
           "ec2:DescribeLaunchTemplateVersions"
       ],
       "Effect": "Allow",
       "Resource": "*"
    },
    {
        "Sid": "NetworkingEasyConfig",
        "Action": [
            "ec2:CreateVpc",
            "ec2:ModifyVpcAttribute",
            "ec2:DescribeNatGateways",
            "ec2:CreateNatGateway",
            "ec2:DescribeInternetGateways",
            "ec2:CreateInternetGateway",
            "ec2:AttachInternetGateway",
            "ec2:DescribeRouteTables",
            "ec2:CreateRouteTable",
            "ec2:AssociateRouteTable",
            "ec2:CreateSubnet",
            "ec2:ModifySubnetAttribute"
        ],
        "Effect": "Allow",
        "Resource": "*"
    },
    {
      "Sid": "EC2Modify",
      "Action": [
        "ec2:CreateVolume",
        "ec2:RunInstances",
        "ec2:AllocateAddress",
        "ec2:AssociateAddress",
        "ec2:AttachNetworkInterface",
        "ec2:AuthorizeSecurityGroupEgress",
        "ec2:AuthorizeSecurityGroupIngress",
        "ec2:CreateNetworkInterface",
        "ec2:CreateSecurityGroup",
        "ec2:ModifyVolumeAttribute",
        "ec2:ModifyNetworkInterfaceAttribute",
        "ec2:DeleteNetworkInterface",
        "ec2:DeleteVolume",
        "ec2:TerminateInstances",
        "ec2:DeleteSecurityGroup",
        "ec2:DisassociateAddress",
        "ec2:RevokeSecurityGroupIngress",
        "ec2:ReleaseAddress",
        "ec2:CreatePlacementGroup",
        "ec2:DeletePlacementGroup"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "DynamoDB",
      "Action": [
        "dynamodb:DescribeTable",
        "dynamodb:CreateTable",
        "dynamodb:DeleteTable",
        "dynamodb:TagResource"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:dynamodb:<REGION>:<AWS ACCOUNT ID>:table/parallelcluster-*"
    },
    {
      "Sid": "CloudFormation",
      "Action": [
        "cloudformation:DescribeStackEvents",
        "cloudformation:DescribeStackResource",
        "cloudformation:DescribeStackResources",
        "cloudformation:DescribeStacks",
        "cloudformation:ListStacks",
        "cloudformation:GetTemplate",
        "cloudformation:CreateStack",
        "cloudformation:DeleteStack",
        "cloudformation:UpdateStack"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:cloudformation:<REGION>:<AWS ACCOUNT ID>:stack/parallelcluster-*"
    },
    {
      "Sid": "SQS",
      "Action": [
        "sqs:GetQueueAttributes",
        "sqs:CreateQueue",
        "sqs:SetQueueAttributes",
        "sqs:DeleteQueue",
        "sqs:TagQueue"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "SQSQueue",
      "Action": [
        "sqs:SendMessage",
        "sqs:ReceiveMessage",
        "sqs:ChangeMessageVisibility",
        "sqs:DeleteMessage",
        "sqs:GetQueueUrl"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:sqs:<REGION>:<AWS ACCOUNT ID>:parallelcluster-*"
    },
    {
      "Sid": "SNS",
      "Action": [
        "sns:ListTopics",
        "sns:GetTopicAttributes",
        "sns:CreateTopic",
        "sns:Subscribe",
        "sns:DeleteTopic"],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "IAMRole",
      "Action": [
        "iam:PassRole",
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:GetRole",
        "iam:TagRole",
        "iam:SimulatePrincipalPolicy"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:role/parallelcluster-*"
    },
    {
      "Sid": "IAMInstanceProfile",
      "Action": [
        "iam:CreateInstanceProfile",
        "iam:DeleteInstanceProfile",
        "iam:GetInstanceProfile",
        "iam:PassRole"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:iam::<AWS ACCOUNT ID>:instance-profile/*"
    },
    {
      "Sid": "IAM",
      "Action": [
        "iam:AddRoleToInstanceProfile",
        "iam:RemoveRoleFromInstanceProfile",
        "iam:GetRolePolicy",
        "iam:PutRolePolicy",
        "iam:DeleteRolePolicy",
        "iam:GetPolicy",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "S3ResourcesBucket",
      "Action": ["s3:*"],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::parallelcluster-*"]
    },
    {
      "Sid": "S3ParallelClusterReadOnly",
      "Action": [
        "s3:Get*",
        "s3:List*"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::<REGION>-aws-parallelcluster/*"]
    },
    {
      "Sid": "Lambda",
      "Action": [
        "lambda:CreateFunction",
        "lambda:DeleteFunction",
        "lambda:GetFunctionConfiguration",
        "lambda:InvokeFunction",
        "lambda:AddPermission",
        "lambda:RemovePermission"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:lambda:<REGION>:<AWS ACCOUNT ID>:function:parallelcluster-*"
    },
    {
      "Sid": "Logs",
      "Effect": "Allow",
      "Action": ["logs:*"],
      "Resource": "arn:aws:logs:<REGION>:<AWS ACCOUNT ID>:*"
    },
    {
      "Sid": "CodeBuild",
      "Effect": "Allow",
      "Action": ["codebuild:*"],
      "Resource": "arn:aws:codebuild:<REGION>:<AWS ACCOUNT ID>:project/parallelcluster-*"
    },
    {
      "Sid": "ECR",
      "Effect": "Allow",
      "Action": ["ecr:*"],
      "Resource": "*"
    },
    {
      "Sid": "Batch",
      "Effect": "Allow",
      "Action": ["batch:*"],
      "Resource": "*"
    },
    {
      "Sid": "AmazonCloudWatchEvents",
      "Effect": "Allow",
      "Action": ["events:*"],
      "Resource": "*"
    }
  ]
}
```