# `pcluster configure`<a name="pcluster.configure"></a>

Begins an AWS ParallelCluster configuration\.

```
pcluster configure [ -h ] [ -c CONFIG_FILE ] [ -r REGION ]
```

## Named arguments<a name="pcluster.configure.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster configure`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the full path of the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.  
For more information, see [Configuring AWS ParallelCluster](getting-started-configuring-parallelcluster.md)\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. If this is specified, the configuration will skip the Region detection\.

If [`pcluster configure`](#pcluster.configure) created a new VPC, you can delete that VPC by deleting the AWS CloudFormation stack it created\. The name will start with "parallelclusternetworking\-" and contain the creation time in a "YYYYMMDDHHMMSS" format\. You can list the stacks using the [list\-stacks](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks) command\.

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