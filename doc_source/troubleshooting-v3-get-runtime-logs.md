# Retrieve AWS ParallelCluster UI and AWS ParallelCluster runtime logs<a name="troubleshooting-v3-get-runtime-logs"></a>

Learn how to retrieve the AWS ParallelCluster UI and AWS ParallelCluster runtime logs for troubleshooting\. To start, find the relevant AWS ParallelCluster UI and AWS ParallelCluster stack names\. Use the stack name to locate the installation log groups\. To finish, export the logs\. These logs are specific to the AWS ParallelCluster runtime\. For cluster logs, see [Retrieving and preserving logs](troubleshooting-v3-get-logs.md)\.

**Prerequisites**
+ The AWS CLI is installed\.
+ You have credentials to run AWS CLI commands on the AWS account that the AWS ParallelCluster UI is on\.
+ You can access the Amazon CloudWatch console on the AWS account that the AWS ParallelCluster UI is on\.

## Step 1: Locate the stack names for the relevant stacks<a name="pcui-install-logs-v3-step-1"></a>

In the following example, replace the red highlighted text with your actual values\.

List the stacks, using the AWS Region where you installed the AWS ParallelCluster UI:

```
$ aws cloudformation list-stacks --region aws-region-id
```

Note the stack names for the following stacks:
+ The name of the stack that deployed the AWS ParallelCluster UI on your account\. You entered this name when you installed the AWS ParallelCluster UI; for example, `pcluster-ui`\.
+ The AWS ParallelCluster stack that is prefixed with the stack name you entered; for example, `pcluster-ui-ParallelClusterApi-ABCD1234EFGH`\.

## Step 2: Locate the log groups<a name="pcui-install-logs-v3-step-2"></a>

List the log groups of the AWS ParallelCluster UI stack, as shown in the following example:

```
$ aws cloudformation describe-stack-resources \
   --region aws-region-id \
   --stack-name pcluster-ui \
   --query "StackResources[?ResourceType == 'AWS::Logs::LogGroup' && (LogicalResourceId == 'ApiGatewayAccessLog' || LogicalResourceId == 'ParallelClusterUILambdaLogGroup')].PhysicalResourceId" \
   --output text
```

List the log groups of the AWS ParallelCluster API stack, as shown in the following example:

```
$ aws cloudformation describe-stack-resources \
   --region aws-region-id \
   --stack-name pcluster-ui-ParallelCluster-Api-ABCD1234EFGH \
   --query "StackResources[?ResourceType == 'AWS::Logs::LogGroup' && LogicalResourceId == 'ParallelClusterFunctionLogGroup'].PhysicalResourceId" \
   --output text
```

Note the lists of log groups for use in the next step\.

## Step 3: Export the logs<a name="pcui-install-logs-v3-step-3"></a>

Use the following steps to gather and export the logs:

1. Log in to the AWS Management Console, and then navigate to the [Amazon CloudWatch](https://console.aws.amazon.com/cloudwatch/) console on the AWS account that the AWS ParallelCluster UI is on\.

1. Choose **Logs**, **Logs Insights** in the navigation pane\.

1. Select all of the log groups listed in the previous step\.

1. Choose a time range, such as 12 hours\.

1. Run the following query:

   ```
   $ fields @timestamp, @message
   | sort @timestamp desc
   | limit 10000
   ```

1. Choose **Export results**, **Download table \(JSON\)**\.