# Creating a cluster with AWS CloudFormation<a name="tutorials_09_cfn-custom-resource-v3"></a>

Learn how to create a cluster with an AWS ParallelCluster CloudFormation custom resource\. For more information, see [AWS CloudFormation custom resource](cloudformation-v3.md)\.

When using AWS ParallelCluster, you only pay for the AWS resources that are created when you create or update AWS ParallelCluster images and clusters\. For more information, see [AWS services used by AWS ParallelCluster](aws-services-v3.md)\.

**Prerequisites:**
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
+ An [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ An IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) that are required to run the [`pcluster`](pcluster-v3.md) CLI\.

## Cluster creation with a CloudFormation quick\-create stack<a name="cfn-custom-resource-quick-v3"></a>

In this tutorial, you use a quick\-create stack to deploy a CloudFormation template that creates a cluster and the following AWS resources:
+ A root CloudFormation stack created by using a CloudFormation quick\-create stack\.
+ Nested CloudFormation stacks that include default policies, default VPC set up, and a custom resource provider\.
+ An example AWS ParallelCluster cluster stack and a cluster that you can log in to and run jobs\.

**Create a cluster with AWS CloudFormation**

1. Sign in to the AWS Management Console\.

1. Open the CloudFormation [quick\-create link](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=mycluster&templateURL=https://us-east-1-aws-parallelcluster.s3.amazonaws.com/parallelcluster/3.6.0/templates/1-click/cluster-example.yaml) to create the following resources in the CloudFormation console:
   + A nested CloudFormation stack with a VPC with a public and private subnet for running the cluster head node and compute nodes, respectively\.
   + A nested CloudFormation stack with an AWS ParallelCluster custom resource for managing the cluster\.
   + A nested CloudFormation stack with the default policies for managing the cluster\.
   + A root CloudFormation stack for the nested stacks\.
   + An AWS ParallelCluster cluster with the Slurm scheduler and a defined number of compute nodes\.  
![\[The console CloudFormation quick-create user interface.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/cfn-quick-create.png)

1. In the **Quick create stack** **Parameters** section, enter values for the following parameters:

   1. For **KeyName**, enter the name of your EC2 key pair\.

   1. For **AvailabilityZone**, choose an AZ for your cluster nodes, for example, `us-east-1a`\.

1. Check the boxes to acknowledge each of the access capabilities at the bottom of the page\.

1. Choose **Create stack**\.

1. Wait for the CloudFormation stack to reach the `CREATE_COMPLETE` state\.

## Cluster creation with the AWS CloudFormation Command Line Interface \(CLI\)<a name="cfn-custom-resource-cli-v3"></a>

In this tutorial, you use the AWS Command Line Interface \(CLI\) for CloudFormation to deploy a CloudFormation template that creates a cluster\.

**Create the following AWS resources:**
+ A root CloudFormation stack created by using a CloudFormation quick\-create stack\.
+ Nested CloudFormation stacks that include default policies, default VPC setup, and a custom resource provider\.
+ An example AWS ParallelCluster cluster stack and a cluster that you can log in to and run jobs\.

Replace *inputs highlighted in red*, such as *keypair*, with your own values\.

**Create a cluster with AWS CloudFormation**

1. Create a CloudFormation template named `cluster_template.yaml` with the following content:

   ```
   AWSTemplateFormatVersion: '2010-09-09'
   Description: >
     AWS ParallelCluster CloudFormation Template
   
   Parameters:
     KeyName:
       Description: KeyPair to login to the head node
       Type: AWS::EC2::KeyPair::KeyName
   
     AvailabilityZone:
       Description: Availability zone where instances will be launched
       Type: AWS::EC2::AvailabilityZone::Name
       Default: us-east-2a
   
   Mappings:
     ParallelCluster:
       Constants:
         Version: 3.6.0
   
   Resources:
     PclusterClusterProvider:
       Type: AWS::CloudFormation::Stack
       Properties:
         TemplateURL: !Sub
           - https://${AWS::Region}-aws-parallelcluster.s3.${AWS::Region}.${AWS::URLSuffix}/parallelcluster/${Version}/templates/custom_resource/cluster.yaml
           - { Version: !FindInMap [ParallelCluster, Constants, Version] }
   
     PclusterVpc:
       Type: AWS::CloudFormation::Stack
       Properties:
         Parameters:
           PublicCIDR: 10.0.0.0/24
           PrivateCIDR: 10.0.16.0/20
           AvailabilityZone: !Ref AvailabilityZone
         TemplateURL: !Sub
           - https://${AWS::Region}-aws-parallelcluster.s3.${AWS::Region}.${AWS::URLSuffix}/parallelcluster/${Version}/templates/networking/public-private-${Version}.cfn.json
           - { Version: !FindInMap [ParallelCluster, Constants, Version ] }
   
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
               SubnetId: !GetAtt [ PclusterVpc , Outputs.PublicSubnetId ]
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
                 -  !GetAtt [ PclusterVpc , Outputs.PrivateSubnetId ]
   Outputs:
     HeadNodeIp:
       Description: The Public IP address of the HeadNode
       Value: !GetAtt [ PclusterCluster, headNode.publicIpAddress ]
   ```

1. Run the following AWS CLI command to deploy the CloudFormation stack for cluster creation and management\.

   ```
   $ aws cloudformation deploy --template-file ./cluster_template.yaml \
     --stack-name mycluster \
     --parameter-overrides KeyName=keypair \
                           AvailabilityZone=us-east-2b \
     --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
   ```

## View CloudFormation cluster output<a name="cfn-custom-resource-view-v3"></a>

View the CloudFormation cluster output to obtain useful cluster details\. The added `ValidationMessages` property provides access to validation messages from cluster create and update operations\.

1. Navigate to the [CloudFormation console](https://console.aws.amazon.com/cloudformation/home) and select the stack that includes your AWS ParallelCluster custom resource\.

1. Choose **Stack details**, and select the **Outputs** tab\.  
![\[The console CloudFormation outputs table showing values for HeadNodeIp and ValidationMessages.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/cfn-outputs.png)

   Validation messages might be truncated\. For more information about how to retrieve logs, see [AWS ParallelCluster troubleshooting](troubleshooting-v3.md)\.

## Access your cluster<a name="cfn-custom-resource-access-v3"></a>

Access the cluster\.

**`ssh` into the cluster head node**

1. After the CloudFormation stack deployment is complete, obtain the IP address of the head node with the following command:

   ```
   $ HEAD_NODE_IP=$(aws cloudformation describe-stacks --stack-name=mycluster --query "Stacks|[0].Outputs[?OutputKey=='HeadNodeIp']|[0].OutputValue" --output=text)
   ```

   You can also retrieve the head node IP address from **HeadNodeIp** parameter in the cluster stack **Outputs** tab in the CloudFormation console\.

   You can find the head node IP address here because it was added in the `Outputs` section of the cluster CloudFormation template, specifically for this example cluster\.

1. Connect to the cluster head node by running the following command:

   ```
   $ ssh -i keyname.pem ec2-user@$HEAD_NODE_IP
   ```

## Clean up<a name="cfn-custom-resource-cleanup-v3"></a>

Delete the cluster\.

1. Run the following AWS CLI command to delete the CloudFormation stack and cluster\.

   ```
   $ aws cloudformation delete-stack --stack-name=mycluster
   ```

1. Check the stack delete status by running the following command\.

   ```
   $ aws cloudformation describe-stacks --stack-name=mycluster
   ```