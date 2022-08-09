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

**Example using AWS ParallelCluster version 2\.11\.7:**

```
$ pcluster delete -c path/to/config -r us-east-1 mycluster
Deleting: mycluster
Status: RootRole - DELETE_COMPLETE                                              
Cluster deleted successfully.
```

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