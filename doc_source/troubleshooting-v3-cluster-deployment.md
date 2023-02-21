# Troubleshooting cluster deployment issues<a name="troubleshooting-v3-cluster-deployment"></a>

If your cluster fails to be created and rolls back stack creation, you can look through the log files to diagnose the issue\. The failure message likely looks like the following output:

```
$ pcluster create-cluster --cluster-name mycluster --region eu-west-1 \
 --cluster-configuration cluster-config.yaml
{
  "cluster": {
    "clusterName": "mycluster",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f01-11ec-a3b9-024fcc6f3387",
    "region": "eu-west-1",
    "version": "3.5.0",
    "clusterStatus": "CREATE_IN_PROGRESS"
  }
}

$ pcluster describe-cluster --cluster-name mycluster --region eu-west-1
{
  "creationTime": "2021-09-06T11:03:47.696Z",
  ...
  "cloudFormationStackStatus": "ROLLBACK_IN_PROGRESS",
  "clusterName": "mycluster",
  "computeFleetStatus": "UNKNOWN",
  "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f01-11ec-a3b9-024fcc6f3387",
  "lastUpdatedTime": "2021-09-06T11:03:47.696Z",
  "region": "eu-west-1",
  "clusterStatus": "CREATE_FAILED"
}
```

**Topics**
+ [View AWS CloudFormation events on `CREATE_FAILED`](#troubleshooting-v3-cluster-deployment-events)
+ [Use the CLI to view log streams](#troubleshooting-v3-cluster-deployment-cli-logstreams)
+ [Re\-create the failed cluster with `rollback-on-failure`](#troubleshooting-v3-cluster-deployment-cli-fail-rollback)

## View AWS CloudFormation events on `CREATE_FAILED`<a name="troubleshooting-v3-cluster-deployment-events"></a>

You can use the console or the AWS ParallelCluster CLI to view CloudFormation events on `CREATE_FAILED` errors to help find the root cause\.

**Topics**
+ [View events in the CloudFormation console](#troubleshooting-v3-cluster-deployment-cloudformation)
+ [Use the CLI to view and filter CloudFormation events on `CREATE_FAILED`](#troubleshooting-v3-cluster-deployment-cli-events)

### View events in the CloudFormation console<a name="troubleshooting-v3-cluster-deployment-cloudformation"></a>

To see more information about what caused the `"CREATE_FAILED"` status, you can use the CloudFormation console\.

**View CloudFormation error messages from the console\.**

1. Log in to the AWS Management Console and navigate to [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Select the stack named *cluster\_name*\.

1. Choose the **Events** tab\.

1. Check the **Status** for the resource that failed to create by scrolling through the list of resource events by **Logical ID**\. If a subtask failed to create, work backwards to find the failed resource event\.

1. As an example, if you see the following status message, you must use instance types that won't exceed your current vCPU limit or request more vCPU capacity\.

   ```
   2022-02-04 16:09:44 UTC-0800	HeadNode	CREATE_FAILED	You have requested more vCPU capacity than your current vCPU limit of 0 allows 
        for the instance bucket that the specified instance type belongs to. Please visit http://aws.amazon.com/contact-us/ec2-request to request an adjustment to this limit. 
        (Service: AmazonEC2; Status Code: 400; Error Code: VcpuLimitExceeded; Request ID: a9876543-b321-c765-d432-dcba98766789; Proxy: null).
   ```

### Use the CLI to view and filter CloudFormation events on `CREATE_FAILED`<a name="troubleshooting-v3-cluster-deployment-cli-events"></a>

To diagnose the cluster creation issue, you can use the [`pcluster get-cluster-stack-events`](pcluster.get-cluster-stack-events-v3.md) command by filtering for `CREATE_FAILED` status\. For more information, see [Filtering AWS CLI output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-filter.html) in the *AWS Command Line Interface User Guide*\.

```
$ pcluster get-cluster-stack-events --cluster-name mycluster --region eu-west-1 \
    --query 'events[?resourceStatus==`CREATE_FAILED`]'
  [
    {
      "eventId": "3ccdedd0-0f03-11ec-8c06-02c352fe2ef9",
      "physicalResourceId": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f02-11ec-a3b9-024fcc6f3387",
      "resourceStatus": "CREATE_FAILED",
      "resourceStatusReason": "The following resource(s) failed to create: [HeadNode]. ",
      "stackId": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f02-11ec-a3b9-024fcc6f3387",
      "stackName": "mycluster",
      "logicalResourceId": "mycluster",
      "resourceType": "AWS::CloudFormation::Stack",
      "timestamp": "2021-09-06T11:11:51.780Z"
    },
    {
      "eventId": "HeadNode-CREATE_FAILED-2021-09-06T11:11:50.127Z",
      "physicalResourceId": "i-04e91cc1f4ea796fe",
      "resourceStatus": "CREATE_FAILED",
      "resourceStatusReason": "Received FAILURE signal with UniqueId i-04e91cc1f4ea796fe",
      "resourceProperties": "{\"LaunchTemplate\":{\"Version\":\"1\",\"LaunchTemplateId\":\"lt-057d2b1e687f05a62\"}}",
      "stackId": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f02-11ec-a3b9-024fcc6f3387",
      "stackName": "mycluster",
      "logicalResourceId": "HeadNode",
      "resourceType": "AWS::EC2::Instance",
      "timestamp": "2021-09-06T11:11:50.127Z"
    }
  ]
```

In the previous example, the failure was in the head node setup\.

## Use the CLI to view log streams<a name="troubleshooting-v3-cluster-deployment-cli-logstreams"></a>

To debug this kind of issue, you can list the log streams available from the head node with the [`pcluster list-cluster-log-streams`](pcluster.list-cluster-log-streams-v3.md) by filtering for `node-type` and then analyzing the log streams content\.

```
$ pcluster list-cluster-log-streams --cluster-name mycluster --region eu-west-1 \
--filters 'Name=node-type,Values=HeadNode'
{
  "logStreams": [
    {
      "logStreamArn": "arn:aws:logs:eu-west-1:xxx:log-group:/aws/parallelcluster/mycluster-202109061103:log-stream:ip-10-0-0-13.i-04e91cc1f4ea796fe.cfn-init",
      "logStreamName": "ip-10-0-0-13.i-04e91cc1f4ea796fe.cfn-init",
      ...
    },
    {
      "logStreamArn": "arn:aws:logs:eu-west-1:xxx:log-group:/aws/parallelcluster/mycluster-202109061103:log-stream:ip-10-0-0-13.i-04e91cc1f4ea796fe.chef-client",
      "logStreamName": "ip-10-0-0-13.i-04e91cc1f4ea796fe.chef-client",
      ...
    },
    {
      "logStreamArn": "arn:aws:logs:eu-west-1:xxx:log-group:/aws/parallelcluster/mycluster-202109061103:log-stream:ip-10-0-0-13.i-04e91cc1f4ea796fe.cloud-init",
      "logStreamName": "ip-10-0-0-13.i-04e91cc1f4ea796fe.cloud-init",
      ...
    },
    ...
  ]
}
```

The two primary log streams that you can use to find initialization errors are the following:
+  `cfn-init` is the log for the `cfn-init` script\. First check this log stream\. You're likely to see the `Command chef failed` error in this log\. Look at the lines immediately before this line for more specifics connected with the error message\. For more information, see [cfn\-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html)\.
+  `cloud-init` is the log for [cloud\-init](https://cloudinit.readthedocs.io/)\. If you don't see anything in `cfn-init`, then try checking this log next\.

You can retrieve the content of the log stream by using the [`pcluster get-cluster-log-events`](pcluster.get-cluster-log-events-v3.md) \(note the `--limit 5` option to limit the number of retrieved events\):

```
$ pcluster get-cluster-log-events --cluster-name mycluster \
  --region eu-west-1 --log-stream-name ip-10-0-0-13.i-04e91cc1f4ea796fe.cfn-init \
  --limit 5
{
  "nextToken": "f/36370880979637159565202782352491087067973952362220945409/s",
  "prevToken": "b/36370880752972385367337528725601470541902663176996585497/s",
  "events": [
    {
      "message": "2021-09-06 11:11:39,049 [ERROR] Unhandled exception during build: Command runpostinstall failed",
      "timestamp": "2021-09-06T11:11:39.049Z"
    },
    {
      "message": "Traceback (most recent call last):\n  File \"/opt/aws/bin/cfn-init\", line 176, in <module>\n    worklog.build(metadata, configSets)\n  File \"/usr/lib/python3.7/site-packages/cfnbootstrap/construction.py\", line 135, in build\n    Contractor(metadata).build(configSets, self)\n  File \"/usr/lib/python3.7/site-packages/cfnbootstrap/construction.py\", line 561, in build\n    self.run_config(config, worklog)\n  File \"/usr/lib/python3.7/site-packages/cfnbootstrap/construction.py\", line 573, in run_config\n    CloudFormationCarpenter(config, self._auth_config).build(worklog)\n  File \"/usr/lib/python3.7/site-packages/cfnbootstrap/construction.py\", line 273, in build\n    self._config.commands)\n  File \"/usr/lib/python3.7/site-packages/cfnbootstrap/command_tool.py\", line 127, in apply\n    raise ToolError(u\"Command %s failed\" % name)",
      "timestamp": "2021-09-06T11:11:39.049Z"
    },
    {
      "message": "cfnbootstrap.construction_errors.ToolError: Command runpostinstall failed",
      "timestamp": "2021-09-06T11:11:39.049Z"
    },
    {
      "message": "2021-09-06 11:11:49,212 [DEBUG] CloudFormation client initialized with endpoint https://cloudformation.eu-west-1.amazonaws.com",
      "timestamp": "2021-09-06T11:11:49.212Z"
    },
    {
      "message": "2021-09-06 11:11:49,213 [DEBUG] Signaling resource HeadNode in stack mycluster with unique ID i-04e91cc1f4ea796fe and status FAILURE",
      "timestamp": "2021-09-06T11:11:49.213Z"
    }
  ]
}
```

In the previous example, the failure is caused by a `runpostinstall` failure, so it is strictly related to the content of the custom bootstrap script used in the `OnNodeConfigured` configuration parameter of the [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions)\.

## Re\-create the failed cluster with `rollback-on-failure`<a name="troubleshooting-v3-cluster-deployment-cli-fail-rollback"></a>

AWS ParallelCluster creates cluster CloudWatch log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\. If there are no log streams available, the failure might be caused by the [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) custom bootstrap script or an AMI\-related issue\. To diagnose the creation issue in this case, create the cluster again using [`pcluster create-cluster`](pcluster.create-cluster-v3.md), including the `--rollback-on-failure` parameter set to `false`\. Then, use SSH to view the cluster, as shown in the following:

```
$ pcluster create-cluster --cluster-name mycluster --region eu-west-1 \
   --cluster-configuration cluster-config.yaml --rollback-on-failure false
 {
   "cluster": {
     "clusterName": "mycluster",
     "cloudformationStackStatus": "CREATE_IN_PROGRESS",
     "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f01-11ec-a3b9-024fcc6f3387",
     "region": "eu-west-1",
     "version": "3.5.0",
     "clusterStatus": "CREATE_IN_PROGRESS"
   }
 } 
 $ pcluster ssh --cluster-name mycluster
```

After you're logged into the head node, you should find three primary log files that you can use to find the error\.
+  `/var/log/cfn-init.log` is the log for the `cfn-init` script\. First check this log\. You're likely to see an error such as `Command chef failed` in this log\. Look at the lines immediately before this line for more specifics connected with the error message\. For more information, see [cfn\-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html)\.
+  `/var/log/cloud-init.log` is the log for [cloud\-init](https://cloudinit.readthedocs.io/)\. If you don't see anything in `cfn-init.log`, then try checking this log next\. 
+  `/var/log/cloud-init-output.log` is the output of commands that were run by [cloud\-init](https://cloudinit.readthedocs.io/)\. This includes the output from `cfn-init`\. In most cases, you don't need to look at this log to troubleshoot this type of issue\.