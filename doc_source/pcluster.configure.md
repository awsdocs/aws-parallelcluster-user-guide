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
Specifies the AWS Region to use\. If this is specified, the configuration skips Region detection\.

To delete the network resources in the VPC, you can delete the CloudFormation networking stack\. The stack name starts with "parallelclusternetworking\-" and contains the creation time in "YYYYMMDDHHMMSS" format\. You can list the stacks using the [list\-stacks](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks) command\.

```
$ aws --region us-east-1 cloudformation list-stacks \
   --stack-status-filter "CREATE_COMPLETE" \
   --query "StackSummaries[].StackName" | \
   grep -e "parallelclusternetworking-"
    "parallelclusternetworking-pubpriv-20191029205804"
```

The stack can be deleted using the [delete\-stack](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/DeleteStack) command\.

```
$ aws --region us-east-1 cloudformation delete-stack \
   --stack-name parallelclusternetworking-pubpriv-20191029205804
```

The VPC that [`pcluster configure`](pcluster.configure-v3.md) creates for you is not created in the CloudFormation networking stack\. You can delete that VPC manually in the console or by using the AWS CLI\.

```
$ aws --region us-east-1 ec2 delete-vpc --vpc-id vpc-0b4ad9c4678d3c7ad
```