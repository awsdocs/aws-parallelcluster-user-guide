# Installing the AWS ParallelCluster UI<a name="install-pcui-v3"></a>

The AWS ParallelCluster UI is a web\-based user interface that mirrors the AWS ParallelCluster `pcluster` CLI, while providing a console\-like experience\. You install and access the AWS ParallelCluster UI in your AWS account\. When you run it, the AWS ParallelCluster UI accesses an instance of the AWS ParallelCluster API hosted on Amazon API Gateway in your AWS account\. For more information about the AWS ParallelCluster UI, see [AWS ParallelCluster UI](pcui-using-v3.md)\.

**Prerequisites:**
+ An AWS account
+ Access to the AWS Management Console

**Topics**
+ [Install the AWS ParallelCluster UI](#install-pcui-steps-v3)
+ [Create a custom domain](#install-pcui-api-custom-domain-v3)
+ [Amazon Cognito user pool options](#install-pcui-cognito-v3)
+ [Identify the AWS ParallelCluster and AWS ParallelCluster UI version](#install-pcui-version-v3)
+ [Update the AWS ParallelCluster UI to a new AWS ParallelCluster version](#install-pcui-update-v3)
+ [AWS ParallelCluster UI costs](#install-pcui-costs-v3)

## Install the AWS ParallelCluster UI<a name="install-pcui-steps-v3"></a>

To install an instance of the AWS ParallelCluster UI, you choose an AWS CloudFormation quick\-create link for the AWS Region that you create clusters in\. The quick\-create URL takes you to a **Create Stack Wizard** where you provide quick\-create stack template inputs and deploy the stack\. For more information about CloudFormation quick\-create stacks, see [Creating quick\-create links for stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stacks-quick-create-links.html) in the *AWS CloudFormation User Guide*\.

**Note**  
You can only create and edit clusters or build images with the same AWS ParallelCluster version that you use to install the AWS ParallelCluster UI\.

### AWS ParallelCluster UI quick\-create links by Region<a name="pcui-install-links"></a>


| UI quick\-create links | 
| --- | 
|  [us\-east\-1](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-east\-2](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-west\-1](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-west\-2](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-west\-1](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-west\-2](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-west\-3](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-central\-1](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [eu\-north\-1](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [me\-south\-1](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [sa\-east\-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ca\-central\-1](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-northeast\-1](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-northeast\-2](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-south\-1](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-southeast\-1](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [ap\-southeast\-2](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 
|  [us\-gov\-west\-1](https://console.amazonaws-us-gov.com/cloudformation/home?region=us-gov-west-1#/stacks/create/review?stackName=parallelcluster-ui&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui.yaml)  | 

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

## Create a custom domain<a name="install-pcui-api-custom-domain-v3"></a>

Learn how to create a custom domain for the AWS ParallelCluster UI\. The UI is hosted on Amazon API Gateway in your AWS account\. You can create a custom domain in the API Gateway console\.

**Prerequisites:**
+ You have an AWS account\.
+ You have an AWS ParallelCluster UI instance that you can access\.
+ You own a domain\.
+ You can change basic Domain Name System \(DNS\) settings\.

### Step 1: Create a new domain in the Amazon API Gateway<a name="tutorials_08_custom_domain-v3-step-1"></a>

1. In the AWS Management Console, navigate to [API Gateway](https://console.aws.amazon.com/apigateway/) where you can see your AWS ParallelCluster UI API listed\.

1. In the navigation pane, choose **Custom domain names**\.

1. Choose **Create**\.

1. In **Domain details**, enter your domain name\.

1. In **Endpoint configuration**, choose an existing ACM certificate, or choose **Create a new ACM Certificate**\.

   **\(Optional\) Create a certificate**

   1. In the ACM console, choose **Request**\.

   1. In **Domain names**, enter your domain name\.

   1. In **Validation method**, choose a validation method\.

      If you choose **Email validation**, an email is sent to the email address that is on file with the domain registrar\.

   1. Select **I approve** to activate the certificate\.

### Step 2: Set up API mappings<a name="tutorials_08_custom_domain-v3-step-2"></a>

1. In [API Gateway](https://console.aws.amazon.com/apigateway/), **Custom domain names**, **your\-domain\-name**, choose **Configure API mappings\.**\.

1. Choose **Custom domain names**\.

1. Choose **Add new mapping**\.

1. Choose the AWS ParallelCluster UI **API**, the $default **Stage**, and **Save**\.

1. In **API Gateway domain name**, copy the value for use in the next steps\.

### Step 3: Set up DNS<a name="tutorials_08_custom_domain-v3-step-3"></a>
+ Create a DNS CNAME rule that points your domain to the API Gateway domain\. Enter only the domain\. For example, don't add the stage, such as `beta` or `prod`\. Replace *abcde12345* with your API Gateway API ID, and replace *us\-east\-2* with the API AWS Region\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/install-pcui-v3.html)

### Step 4: Add the domain to your Amazon Cognito user pool<a name="tutorials_08_custom_domain-v3-step-4"></a>

1. Navigate to the [Amazon Cognito console](https://console.aws.amazon.com/cognito/v2/)\.

1. Select your user pool link\.

1. Choose **App integration**\.

1. In **Domain**, choose **Actions**, **Create custom domain**\.

1. Enter your **Custom domain** and select your **ACM certificate**\.

1. Choose **Create custom domain**\.

### Step 5: Configure the API Gateway callback URL<a name="tutorials_08_custom_domain-v3-step-5"></a>

1. Navigate to the [Amazon Cognito console](https://console.aws.amazon.com/cognito/v2/)\.

1. In your Amazon Cognito user pool **App integration**, **App clients and analytics**, select the application link\.

1. In **Hosted UI**, choose **Edit**\.

1. In **Allowed callback URLs**, choose **Add another URL** and enter a callback URL such as `example.com/login`\.

### Step 6: Configure the Lambda function<a name="tutorials_08_custom_domain-v3-step-6"></a>

1. Navigate to the [Lambda console](https://console.aws.amazon.com/lambda/)\.

1. In the navigation pane, choose **Functions**\.

1. Filter the list of functions to find the `ParallelClusterUIFunction` and select the link\.

1. Choose **Configuration**, **Environment variables**\.

1. Choose **Edit**\.

1. For the `SITE_URL` value, enter your custom domain\.

1. Navigate to your domain, such as `example.com`, and authenticate to connect to the AWS ParallelCluster UI\.

## Amazon Cognito user pool options<a name="install-pcui-cognito-v3"></a>

The following sections refer to CloudFormation quick\-create links or quick\-create URLs\. The quick\-create URL takes you to a **Create Stack Wizard** where you provide quick\-create stack template inputs and deploy the stack\. For more information about CloudFormation quick\-create stacks, see [Creating quick\-create links for stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stacks-quick-create-links.html) in the *AWS CloudFormation User Guide*\.

To maintain an Amazon Cognito user pool that you can use with multiple AWS ParallelCluster UI instances, consider the following options:
+ Use an existing AWS ParallelCluster UI instance that links to an Amazon Cognito user pool created from a nested CloudFormation stack\. This is what is created when you deploy the AWS ParallelCluster UI by using the quick\-create link and you keep all Amazon Cognito parameters blank\.
+ Use a standalone Amazon Cognito user pool that’s deployed before the AWS ParallelCluster UI is deployed\. Then, deploy a new AWS ParallelCluster UI instance that's linked to the standalone Amazon Cognito user pool that you have already deployed\. This way, you separate the Amazon Cognito deployment from the AWS ParallelCluster UI deployment\. Furthermore, non\-nested AWS ParallelCluster UI CloudFormation stacks are easier to update\.

### Use an existing Amazon Cognito user pool with a new AWS ParallelCluster UI instance<a name="install-pcui-existing-cognito-v3"></a>

1. In the **CloudFormation console**, select the AWS ParallelCluster UI stack that contains the Amazon Cognito user pool that you want to use with multiple AWS ParallelCluster UI instances\.

1. Navigate to the nested stack that created the Amazon Cognito userpool\.

1. Select the **Outputs** tab\.

1. Copy the values of the following parameters:
   + `UserPoolId`
   + `UserPoolAuthDomain`
   + `SNSRole`

1. Deploy a new AWS ParallelCluster UI instance by using the quick\-create link, and fill in all `External AWS ParallelCluster UI Amazon Cognito` parameters with the outputs that you copied\. This prevents the new AWS ParallelCluster UI stack from creating a new pool and links it to the existing Amazon Cognito user pool that was created from a nested stack\. You can deploy subsequent new AWS ParallelCluster UI instances that have the same parameter values, and you can link them to the Amazon Cognito user pool\.

### Create a standalone Amazon Cognito userpool<a name="install-pcui-standalone-cognito-v3"></a>

#### AWS ParallelCluster UI Amazon Cognito quick\-create links by Region<a name="pcui-cog-install-links"></a>


| UI Amazon Cognito quick\-create links | 
| --- | 
|  [us\-east\-1](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-east\-2](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-west\-1](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-west\-2](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-west\-1](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-west\-2](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-west\-3](https://eu-west-3.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-central\-1](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [eu\-north\-1](https://eu-north-1.console.aws.amazon.com/cloudformation/home?region=eu-north-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [me\-south\-1](https://me-south-1.console.aws.amazon.com/cloudformation/home?region=me-south-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [sa\-east\-1](https://sa-east-1.console.aws.amazon.com/cloudformation/home?region=sa-east-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ca\-central\-1](https://ca-central-1.console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-northeast\-1](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-northeast\-2](https://ap-northeast-2.console.aws.amazon.com/cloudformation/home?region=ap-northeast-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-south\-1](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-southeast\-1](https://ap-southeast-1.console.aws.amazon.com/cloudformation/home?region=ap-southeast-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [ap\-southeast\-2](https://ap-southeast-2.console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 
|  [us\-gov\-west\-1](https://console.amazonaws-us-gov.com/cloudformation/home?region=us-gov-west-1#/stacks/create/review?stackName=parallelcluster-ui-cognito&templateURL=https://parallelcluster-ui-release-artifacts-us-east-1.s3.us-east-1.amazonaws.com/parallelcluster-ui-cognito.yaml)  | 

1. Launch an Amazon Cognito\-only stack by choosing a quick\-create link labeled with same AWS Region in which you deploy your AWS ParallelCluster UI instances\. See the quick\-create links at the beginning of this section\.

1. After stack creation completes, select the **Outputs** tab and copy the values of the following parameters:
   + `UserPoolId`
   + `UserPoolAuthDomain`
   + `SNSRole`

1. Deploy a new AWS ParallelCluster UI instance by choosing an AWS ParallelCluster UI quick\-start link and filling in all `External AWS ParallelCluster UI Amazon Cognito` parameters with the values that you copied\. The new AWS ParallelCluster UI instance links to the standalone Amazon Cognito user pool and doesn’t create a nested stack or a new user pool\. You can deploy subsequent new AWS ParallelCluster UI instances that have the same parameter values, and you can link them to the standalone Amazon Cognito user pool\.

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