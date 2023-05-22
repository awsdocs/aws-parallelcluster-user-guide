# AWS CloudFormation custom resource<a name="cloudformation-v3"></a>

Starting with AWS ParallelCluster version 3\.6\.0, you can use an AWS ParallelCluster CloudFormation custom resource in an AWS CloudFormation stack\. The custom resource is an AWS ParallelCluster hosted stack\. This way, you can use CloudFormation to configure and manage your clusters\. For example, you can configure cluster external resources such as network, shared storage, and security group infrastructure in a CloudFormation stack\. Furthermore, you can manage your cluster with a CloudFormation infrastructure as code pipeline\.

Add an AWS ParallelCluster custom resource to your CloudFormation template by doing the following: 

1. Add a custom resource provider stack that is owned and hosted by AWS ParallelCluster\.

1. Reference the provider stack in your CloudFormation template as a custom resource\.

The custom resource provider stack handles and responds to CloudFormation requests\. For example, when you deploy your CloudFormation stack, you also configure and create a cluster\. To update a cluster, you update your CloudFormation stack\. You delete a cluster when you delete your stack\. For more information about CloudFormation custom resources, see [Custom resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html) in the *AWS CloudFormation User Guide*\.

**Warning**  
CloudFormation doesn't detect custom resource drift\. Only use CloudFormation to update the cluster configuration and to delete a cluster\.  
You can use the [`pcluster`](pcluster-v3.md) CLI or the [AWS ParallelCluster UI](pcui-using-v3.md) to monitor the state of the cluster or to update the compute fleet, but you must not use them to update the cluster configuration or to delete the cluster\.

**Note**  
We recommend that you add [termination protection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-protect-stacks.html) to your stack to avoid accidental removal\.

## Provider stack hosted by AWS ParallelCluster<a name="cfn-provider-stack"></a>

The custom resource provider stack is formatted as shown in the following CloudFormation template snippet:

```
PclusterClusterProvider:
  Type: AWS::CloudFormation::Stack
  Properties:
    CustomLambdaRole: # (Optional) RoleARN to override default
    AdditionalIamPolicies: # (Optional) comma-separated list of IAM policies to add
    TemplateURL: !Sub
      - https://${AWS::Region}-aws-parallelcluster.s3.${AWS::Region}.${AWS::URLSuffix}/parallelcluster/${Version}/templates/custom_resource/cluster.yaml
      - { Version: 3.6.0 }
```

**Properties: **    
**CustomLambdaRole \(optional\): **  
A custom role with permissions to run the AWS Lambda that creates and manages the cluster\. By default, the role uses the same policies defined by default in the [AWS ParallelCluster documentation](iam-roles-in-parallelcluster-v3.md)\.  
**AdditionalIamPolicies \(optional\): **  
A comma\-separated list of additional IAM Policy Amazon Resource Names \(ARNs\) to add to the role that the Lambda uses\. This is only used if a `CustomLambdaRole` isn't specified and can be kept blank\.  
If you need additional policies for the head node, compute nodes, or for access to an Amazon S3 bucket, add them to the `CustomLambdaRole` or `AdditionalIamPolicy` property\.  
For more information about the default policies, see [AWS Identity and Access Management permissions in AWS ParallelCluster 3\.x](iam-roles-in-parallelcluster-v3.md)\.  
**TemplateURL \(required\): **  
The AWS ParallelCluster custom resource file URL\.

**Outputs: **    
**ServiceToken: **  
A value that can be used as a custom resource `ServiceToken` property\. A custom resource `ServiceToken` specifies where AWS CloudFormation sends requests\. This is a required input for a cluster resource that you include in your AWS CloudFormation template\.  
**LogGroupArn: **  
The ARN of the CloudWatch `LogGroup` that the underlying resource logs to\.  
**LambdaLayerArn: **  
 The ARN of the Lambda layer that's used for running AWS ParallelCluster operations\.

## Cluster resource<a name="cfn-cluster-resource"></a>

The CloudFormation cluster resource is formatted as shown in the following CloudFormation template snippet:

```
PclusterCluster:
  Type: Custom::PclusterCluster
  Properties:
    ServiceToken: !GetAtt [ PclusterClusterProvider , Outputs.ServiceToken ]
    ClusterName: !Sub 'c-${AWS::StackName}' # Must be different from StackName
    ClusterConfiguration:
    # Your Cluster Configuration
```

**Properties: **    
**ServiceToken: **  
The AWS ParallelCluster provider stack `ServiceToken` output\.  
**ClusterName: **  
The name of the cluster to be created and managed\. The name must not match the CloudFormation stack’s name\. The name can't be changed after the cluster has been created\.  
**ClusterConfiguration: **  
The cluster configuration YAML file, as described in [Cluster configuration file](cluster-configuration-file-v3.md)\. However, you can use the usual CloudFormation constructs, such as [Intrinsic functions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html)\.  
**DeletionPolicy: **  
Defines whether to delete the cluster when the root stack is deleted\. The default is `Delete`\.    
**Retain: **  
Retain the cluster if the custom resource is deleted\.  
To keep the retained cluster functioning, cluster\-dependent resources, such as storage and networking, must have a deletion policy set to retain\.  
**Delete: **  
Delete the cluster if the custom resource is deleted\.

**`Fn::GetAtt` return values: **  
The `Fn::GetAtt` intrinsic function returns a value for a specified attribute of a type\. For more information about using the `Fn::GetAtt intrinsic` function, see [Fn::GetAtt](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getatt.html)\.    
**ClusterProperties: **  
The values from the [`pcluster describe-cluster`](pcluster.describe-cluster-v3.md) operation\.  
**validationMessages: **  
A string containing all the validation messages that occurred during the last create or update operation\.  
**logGroupName: **  
The name of the log group that's used for logging Lambda cluster operations\. The log events are retained for 90 days and the log group is retained after cluster deletion\.

**Example: **`Fn::GetAtt`:

```
# Provide the public IP address of the head node as an output of a stack
Outputs:
  HeadNodeIp:
    Description: The public IP address of the head node
    Value: !GetAtt [ PclusterCluster, headNode.publicIpAddress ]
```

**Example: **Simple, complete CloudFormation template with an AWS ParallelCluster custom resource:

```
AWSTemplateFormatVersion: '2010-09-09'
Description: >
  AWS ParallelCluster CloudFormation Template

Parameters:
  HeadNodeSubnet:
    Description: Subnet where the HeadNode will run
    Type: AWS::EC2::Subnet::Id

  ComputeSubnet:
    Description: Subnet where the Compute Nodes will run
    Type: AWS::EC2::Subnet::Id

  KeyName:
    Description: KeyPair to login to the head node
    Type: AWS::EC2::KeyPair::KeyName

Resources:
  PclusterClusterProvider:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub
        - https://${AWS::Region}-aws-parallelcluster.s3.${AWS::Region}.${AWS::URLSuffix}/parallelcluster/${Version}/templates/custom_resource/cluster.yaml
        - { Version: 3.6.0 }

  PclusterCluster:
    Type: Custom::PclusterCluster
    Properties:
      ServiceToken: !GetAtt [ PclusterClusterProvider , Outputs.ServiceToken ]
      ClusterName: !Sub 'c-${AWS::StackName}'
      ClusterConfiguration:
        Image:
          Os: alinux2
        HeadNode:
          InstanceType: t2.medium
          Networking:
            SubnetId: !Ref HeadNodeSubnet
          Ssh:
            KeyName: !Ref KeyName
        Scheduling:
          Scheduler: slurm
          SlurmQueues:
          - Name: queue0
            ComputeResources:
            - Name: queue0-cr0
              InstanceType: t2.micro
            Networking:
              SubnetIds:
              - !Ref ComputeSubnet

Outputs:
  HeadNodeIp:
    Description: The Public IP address of the HeadNode
    Value: !GetAtt [ PclusterCluster, headNode.publicIpAddress ]
  ValidationMessages:
    Description: Any warnings from cluster create or update operations.
    Value: !GetAtt PclusterCluster.validationMessages
```

To learn more about how to use the CloudFormation AWS ParallelCluster custom resource, see [Creating a cluster with AWS CloudFormation](tutorials_09_cfn-custom-resource-v3.md)\.

## Cluster operations<a name="cfn-cluster-ops"></a>

When a cluster custom resource is added to a CloudFormation stack, CloudFormation can perform the following cluster operations:
+ CloudFormation creates a cluster in a new separate stack when it deploys a stack that includes the AWS ParallelCluster custom resource\.
+ If you update the cluster configuration defined in the stack, according to configuration update policies, CloudFormation updates the cluster\. The AWS ParallelCluster custom resource provider doesn't stop the compute fleet before updating the cluster\. We recommend that you use the [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) setting for cluster updates\. This way, you can avoid making explicit `pcluster update-compute-fleet` calls before and after updates when using the AWS ParallelCluster custom resource\.
+ If you delete the stack, the cluster is deleted\.

## Troubleshooting stacks that include the AWS ParallelCluster custom resource<a name="cfn-cluster-ops-troubleshooting"></a>

With an AWS ParallelCluster custom resource, CloudFormation deploys a cluster from a new, separate stack\. You can monitor cluster creation by taking the following steps:

1. Navigate to CloudFormation in the AWS Management Console and choose **Stacks** in the navigation pane\.

1. Choose the stack with the name that you defined for the cluster name\.

1. If the stack state is `ROLLBACK_COMPLETE`, an error occurred during cluster creation\.

1. Choose **Stack details**, and choose the **Events** tab\.

1. Search **Events** on **Logical ID** for the name that you defined for the cluster name\. It has a `Status reason` that gives a reason for an issue\.

1. You can also choose the **Stacks** drop down menu, and then **Deleted** to see the list of deleted stacks\. Select the stack with the cluster name and view **Events** for more details\.

1. To view the output from the custom resource provider that manages the cluster, select the stack with the **Description** "AWS ParallelCluster Cluster Custom Resource\." Choose the **Resources** tab, find the resource with **Logical ID** `PclusterCfnFunctionLogGroup`, and follow the given link\. View the log streams that show the Lambda debug output\.

1. To troubleshoot the cluster, see [AWS ParallelCluster troubleshooting](troubleshooting-v3.md)\.