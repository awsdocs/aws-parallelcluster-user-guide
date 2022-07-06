# Automated<a name="tutorials_05_multi-user-ad-step1-automated"></a>

1. Sign in to the AWS Management Console

1. Open the link, [CloudFormation Quick Create \(region us\-east\-1\)](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=pcluster-ad&templateURL=https://us-east-1-aws-parallelcluster.s3.amazonaws.com/templates/1-click/ad-integration.yaml), to create the following resources in the CloudFormation console:
   + A VPC with two subnets and routing for public access \(if no VPC is specified\)\.
   + An AWS Managed Microsoft AD\.
   + An EC2 instance that's joined to the AD that you can use to manage the directory\.

1. In the **Quick create stack** page **Parameters** section, enter passwords for the following parameters:
   + **AdminPassword**
   + **ReadOnlyPassword**
   + **UserPassword**

   For **Keypair**, enter the name of an EC2 key pair\.

   Make note of the passwords\. You use them later on in this tutorial\.

1. Acknowledge each of the access capabilities at the bottom of the page by checking the boxes\.

1. Choose **Create stack**\.

1. After the CloudFormation stack has reached the `CREATE_COMPLETE` state, choose the **Outputs** tab of the stack\. Make a note of the output resource names and IDs because you will need to use them in later steps\. The outputs provide the information that's needed to create the cluster:  
![\[A diagram that shows the created stack outputs in the AWS Management Console.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/ad-cfn.png)

1. To complete the exercises [\(Optional\) Step 2: Manage AD users and groups](tutorials_05_multi-user-ad-step2.md), you need the directory ID\. Choose **Resources** and scroll down to make note of the directory ID\.

1. Continue at [\(Optional\) Step 2: Manage AD users and groups](tutorials_05_multi-user-ad-step2.md) or [Step 3: Create the cluster](tutorials_05_multi-user-ad-step3.md)\.