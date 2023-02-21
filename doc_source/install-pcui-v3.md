# Installing the AWS ParallelCluster UI<a name="install-pcui-v3"></a>

The AWS ParallelCluster UI is a web\-based user interface that mirrors the AWS ParallelCluster `pcluster` CLI, while providing a console\-like experience\. You install and access the AWS ParallelCluster UI in your AWS account\. When you run it, the AWS ParallelCluster UI accesses an instance of the AWS ParallelCluster API hosted on Amazon API Gateway in your AWS account\. For more information about the AWS ParallelCluster UI, see [AWS ParallelCluster UI](pcui-using-v3.md)\.

**Prerequisites:**
+ An AWS account
+ Access to the AWS Management Console

## Install the AWS ParallelCluster UI<a name="install-pcui-steps-v3"></a>

To install an instance of the AWS ParallelCluster UI, you choose an AWS CloudFormation quick\-create link for the AWS Region that you create clusters in\. The quick\-create URL takes you to a **Create Stack Wizard** where you provide quick\-create stack template inputs and deploy the stack\. For more information about CloudFormation quick\-create stacks, see [Creating quick\-create links for stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stacks-quick-create-links.html) in the *AWS CloudFormation User Guide*\.

**Note**  
You can only create and edit clusters or build images with the same AWS ParallelCluster version that you use to install the AWS ParallelCluster UI\.

### AWS ParallelCluster UI quick\-create links by Region<a name="pcui-install-links"></a>


|  |  | 
| --- |--- |
| UI quick\-create link | UI quick\-create link alternate | 
|  [us\-east\-1](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [us\-east\-1](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-east\-2](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [us\-east\-2](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-west\-1](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [us\-west\-1](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-west\-2](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [us\-west\-2](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-west\-1](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [eu\-west\-1](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-west\-2](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [eu\-west\-2](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-west\-3](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [eu\-west\-3](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-central\-1](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [eu\-central\-1](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-north\-1](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [eu\-north\-1](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [me\-south\-1](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [me\-south\-1](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [sa\-east\-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [sa\-east\-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ca\-central\-1](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [ca\-central\-1](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-northeast\-1](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [ap\-northeast\-1](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-northeast\-2](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [ap\-northeast\-2](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-south\-1](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [ap\-south\-1](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-southeast\-1](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [ap\-southeast\-1](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-southeast\-2](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  |  [ap\-southeast\-2](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui.yaml)  | 

**Use an AWS CloudFormation quick\-create link to deploy an AWS ParallelCluster UI stack with nested Amazon Cognito, API Gateway, and Amazon EC2 Systems Manager stacks\.**

1. Sign in to the AWS Management Console\.

1. Deploy the AWS ParallelCluster UI by choosing an AWS Region quick\-create link from the table at the start of this section\. This takes you to the CloudFormation **Create Stack Wizard** in the console\.

1. Enter a valid email address for **Admin's Email**\.

   After deployment completes successfully, the AWS ParallelCluster UI sends you a temporary password to this email address\. You use the temporary password to access the AWS ParallelCluster UI\. If you delete the email before you save or use the temporary password, you must delete the stack and reinstall the AWS ParallelCluster UI\.

1. Keep the rest of the form blank or enter values for \(optional\) parameters to customize the AWS ParallelCluster UI build\.

1. Note the stack name for use in later steps\.

1. Navigate to **Capabilities**\. Agree to the CloudFormation capabilities\.

1. Choose **Create**\. It takes about 15 minutes to complete the AWS ParallelCluster API and AWS ParallelCluster UI deployment\.

1. View the stack details as the stack is created\.

1. After the deployment completes, open the admin email that was sent to the address you entered\. It contains a temporary password that you use to access the AWS ParallelCluster UI\. If you permanently delete the email and you haven’t yet logged in to the AWS ParallelCluster UI, you must delete the AWS ParallelCluster UI stack you created and reinstall the AWS ParallelCluster UI\.

1. In the AWS CloudFormation console list of stacks, choose the link to the stack name that you noted in a previous step\.

1. In **Stack details**, choose **Outputs** and select the link for the key named ***Stackname*URL** to open the AWS ParallelCluster UI\. ***Stackname*** is the name that you noted in a previous step\.

1. Enter the temporary password\. Follow the steps to create your own password and log in\.

1. You are now on the home page of the AWS ParallelCluster UI in the AWS Region that you selected\.

1. To get started using the AWS ParallelCluster UI, see [Configure and create a cluster with the AWS ParallelCluster UI](configure-create-pcui-v3.md)\.

## Amazon Cognito user pool options<a name="install-pcui-cognito-v3"></a>

The following sections refer to CloudFormation quick\-create links or quick\-create URLs\. The quick\-create URL takes you to a **Create Stack Wizard** where you provide quick\-create stack template inputs and deploy the stack\. For more information about CloudFormation quick\-create stacks, see [Creating quick\-create links for stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stacks-quick-create-links.html) in the *AWS CloudFormation User Guide*\.

To maintain an Amazon Cognito user pool that you can use with multiple AWS ParallelCluster UI instances, consider the following options:
+ Use an existing AWS ParallelCluster UI installation that links to an Amazon Cognito installation created from a nested stack\. This is what is created when you deploy the AWS ParallelCluster UI by using the quick\-create link and you keep all Amazon Cognito parameters blank\.
+ Use a standalone Amazon Cognito installation that’s deployed before the AWS ParallelCluster UI installation is deployed\. Then, link your AWS ParallelCluster UI instance to the Amazon Cognito installation\. This way, you separate the Amazon Cognito installation from the AWS ParallelCluster UI\. Furthermore, the non\-nested AWS ParallelCluster UI CloudFormation stack is easier to update\.

### Use an existing Amazon Cognito user pool with a new AWS ParallelCluster UI installation<a name="install-pcui-existing-cognito-v3"></a>

1. In the **CloudFormation console**, select the AWS ParallelCluster UI stack that contains the Amazon Cognito user pool that you want to use with multiple AWS ParallelCluster UI instances\.

1. Navigate to the nested stack that created the Amazon Cognito installation\.

1. Select the **Outputs** tab\.

1. Copy the values of the following parameters:
   + `UserPoolId`
   + `UserPoolAuthDomain`
   + `SNSRole`

1. Install a new AWS ParallelCluster UI instance by using the quick\-create link, and fill in all `External AWS ParallelCluster UI Amazon Cognito` parameters with the outputs that you copied\. This prevents the new AWS ParallelCluster UI stack from creating a new pool and links it to the existing Amazon Cognito user pool that was created from a nested stack\. You can install subsequent new AWS ParallelCluster UI instances that have the same parameter values, and you can link them to the standalone Amazon Cognito user pool\.

### Create a standalone Amazon Cognito installation<a name="install-pcui-standalone-cognito-v3"></a>

#### AWS ParallelCluster UI Amazon Cognito quick\-create links by Region<a name="pcui-cog-install-links"></a>


|  |  | 
| --- |--- |
| UI Amazon Cognito quick\-create link | UI Amazon Cognito quick\-create link alternate | 
|  [us\-east\-1](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [us\-east\-1](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-east\-2](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [us\-east\-2](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-west\-1](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [us\-west\-1](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-west\-2](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [us\-west\-2](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-west\-1](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [eu\-west\-1](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-west\-2](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [eu\-west\-2](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-west\-3](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [eu\-west\-3](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-central\-1](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [eu\-central\-1](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-north\-1](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [eu\-north\-1](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [me\-south\-1](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [me\-south\-1](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [sa\-east\-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [sa\-east\-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ca\-central\-1](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [ca\-central\-1](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-northeast\-1](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [ap\-northeast\-1](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-northeast\-2](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [ap\-northeast\-2](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-south\-1](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [ap\-south\-1](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-southeast\-1](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [ap\-southeast\-1](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-southeast\-2](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  |  [ap\-southeast\-2](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-eu-west-1.s3.eu-west-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 

1. Launch an Amazon Cognito\-only stack by choosing a quick\-create link labeled with same AWS Region in which you deploy your AWS ParallelCluster UI instances\. See the quick\-create links at the beginning of this section\.

1. After stack creation completes, select the **Outputs** tab and copy the values of the following parameters:
   + `UserPoolId`
   + `UserPoolAuthDomain`
   + `SNSRole`

1. Install a new AWS ParallelCluster UI instance by choosing an AWS ParallelCluster UI quick\-start link and filling in all `External AWS ParallelCluster UI Amazon Cognito` parameters with the values that you copied\. The new AWS ParallelCluster UI instance links to the standalone Amazon Cognito installation and doesn’t create a nested stack or new pool\. You can install subsequent new AWS ParallelCluster UI instances that have the same parameter values, and you can link them to the standalone Amazon Cognito user pool\.

## Identify the AWS ParallelCluster and AWS ParallelCluster UI version<a name="install-pcui-version-v3"></a>

1. In the CloudFormation console, select an AWS ParallelCluster UI stack\.

1. Select the **Parameters** tab\.

1. The AWS ParallelCluster version is the value of the parameter **Version**\.

1. The AWS ParallelCluster UI version is at the end of the **PublicEcrImageUri** value\. For example, if the value is `public.ecr.aws/pcui/parallelcluster-ui-awslambda:2023.02`, then the version is `2023.02`\.

## Update the AWS ParallelCluster UI to a new AWS ParallelCluster version<a name="install-pcui-update-v3"></a>

To update the AWS ParallelCluster UI to the latest AWS ParallelCluster version, launch a new stack by choosing a [quick\-create link](#install-pcui-steps-v3)\.

## AWS ParallelCluster UI costs<a name="install-pcui-costs-v3"></a>

The AWS ParallelCluster UI is built on a serverless architecture and you can use it within the AWS Free Tier category for most cases\. The following table lists the AWS services that the AWS ParallelCluster UI depends on and their free\-tier limits\. Typical usage is estimated to cost less than one dollar each month\.


| Service | AWS Free Tier | 
| --- | --- | 
|  Amazon Cognito  |  50,000 monthly active users  | 
|  Amazon API Gateway  |  1 million rest API calls  | 
|  AWS Lambda  |  1 million free requests each month and 400,000 GB\-seconds of compute time each month  | 
|  EC2 Image Builder  |  No cost, except EC2  | 
|  Amazon Elastic Compute Cloud  |  15\-minute one\-time container image build  | 
|  AWS CloudFormation  |  5 GB data \(ingestion, archive storage, and data scanned by Logs Insights queries\)  | 