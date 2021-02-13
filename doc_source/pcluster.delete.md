# `pcluster delete`<a name="pcluster.delete"></a>

Deletes a cluster\.

```
pcluster delete [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] [ -nw ] cluster_name
```

## Positional arguments<a name="pcluster.delete.arg"></a>

`cluster_name`  
Specifies the name of the cluster to delete\.

## Named arguments<a name="pcluster.delete.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster delete`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`--keep-logs`  
Keep the CloudWatch Logs data after deleting the cluster\. The log group remains until you delete it manually, but the log events expire based on the [`retention_days`](cw-log-section.md#cw-log-section-retention-days) setting\. The setting defaults to 14 days\.  
Support for the `--keep-logs` argument was added in AWS ParallelCluster version 2\.6\.0\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

When the command is called and begins polling for the status of that call, it's safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.

If [`pcluster configure`](pcluster.configure.md) created a new VPC, you can delete that VPC by deleting the AWS CloudFormation stack it created\. The name will start with "parallelclusternetworking\-" and contain the creation time in a "YYYYMMDDHHMMSS" format\. You can list the stacks using the [list\-stacks](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks) command\.

```
$ aws --region us-east-2 cloudformation list-stacks \
   --stack-status-filter "CREATE_COMPLETE" \
   --query "StackSummaries[].StackName" | \
   grep -e "parallelclusternetworking-"
    "parallelclusternetworking-pubpriv-20191029205804"
```

The stack can be deleted using the [delete\-stack](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/DeleteStack) command\.

```
$ aws --region us-west-2 cloudformation delete-stack \
   --stack-name parallelclusternetworking-pubpriv-20191029205804
```