# AWS ParallelCluster Troubleshooting<a name="troubleshooting-v3"></a>

The AWS ParallelCluster community maintains a Wiki page that provides many troubleshooting tips on the [AWS ParallelCluster GitHub Wiki](https://github.com/aws/aws-parallelcluster/wiki/)\. For a list of known issues, see [Known issues](https://github.com/aws/aws-parallelcluster/wiki#known-issues-)\.

**Topics**
+ [Retrieving and preserving logs](#troubleshooting-v3-get-logs)
+ [Troubleshooting cluster deployment issues](#troubleshooting-v3-cluster-deployment)
+ [Troubleshooting scaling issues](#troubleshooting-v3-scaling-issues)
+ [Placement groups and instance launch issues](#troubleshooting-v3-placemment-groups)
+ [Directories that cannot be replaced](#troubleshooting-v3-dirs-must-keep)
+ [Troubleshooting issues in NICE DCV](#troubleshooting-v3-nice-dcv)
+ [Troubleshooting issues in clusters with AWS Batch integration](#troubleshooting-v3-batch)
+ [Troubleshooting multi\-user integration with Active Directory](#troubleshooting-v3-multi-user)
+ [Troubleshooting custom AMI issues](#troubleshooting-v3-custom-amis)
+ [Troubleshooting a cluster update timeout when `cfn-hup` isn't running](#troubleshooting-v3-cluster-update-timeout)
+ [Network troubleshooting](#troubleshooting-v3-networking)
+ [Additional support](#troubleshooting-v3-additional-support)

## Retrieving and preserving logs<a name="troubleshooting-v3-get-logs"></a>

AWS ParallelCluster creates EC2 metrics for HeadNode and Compute instances and storage\. You can view the metrics in the CloudWatch console **Custom Dashboards**\. AWS ParallelCluster also creates cluster CloudWatch log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. The [Monitoring](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch) cluster configuration section describes how you can modify the cluster CloudWatch logs and dashboard\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

Logs are a useful resource for troubleshooting issues\. For example, if you want to delete a failing cluster, it might be useful to first create an archive of the cluster logs\. Follow the steps in [Archive logs](#troubleshooting-v3-get-logs-archive) to create an archive\.

**Topics**
+ [Cluster logs unavailable in CloudWatch](#troubleshooting-v3-get-logs-unavailable)
+ [Archive logs](#troubleshooting-v3-get-logs-archive)
+ [Preserved logs](#troubleshooting-v3-get-logs-preserve)
+ [Terminated node logs](#troubleshooting-v3-get-logs-terminated-node)

### Cluster logs unavailable in CloudWatch<a name="troubleshooting-v3-get-logs-unavailable"></a>

If cluster logs aren't available in CloudWatch, check to make sure you haven't overwritten the AWS ParallelCluster CloudWatch log configuration when adding custom logs to the configuration\.

To add custom logs to the CloudWatch configuration, make sure you append to the configuration rather than fetch and overwrite it\. For more information on `fetch-config` and `append-config`, see [Multiple CloudWatch agent configuration files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-common-scenarios.html#CloudWatch-Agent-multiple-config-files) in the *CloudWatch User Guide*\.

To restore the AWS ParallelCluster CloudWatch log configuration you can run the following command inside an AWS ParallelCluster node\.

```
$ /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json -s
```

### Archive logs<a name="troubleshooting-v3-get-logs-archive"></a>

You can archive the logs in S3 or in a local file \(depending on the `--output-file` parameter\)\.

**Note**  
You must add permissions to the Amazon S3 bucket policy to grant CloudWatch access\. For more information, see [Set permissions on an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3ExportTasks.html#S3Permissions) in the *CloudWatch Logs User Guide*\.

```
$ pcluster export-cluster-logs --cluster-name mycluster --region eu-west-1 \
  --bucket bucketname --bucket-prefix logs
{
  "url": "https://bucketname.s3.eu-west-1.amazonaws.com/export-log/mycluster-logs-202109071136.tar.gz?..."
}

# use the --output-file parameter to save the logs locally
$ pcluster export-cluster-logs --cluster-name mycluster --region eu-west-1 \
  --bucket bucketname --bucket-prefix logs --output-file /tmp/archive.tar.gz
{
  "path": "/tmp/archive.tar.gz"
}
```

The archive contains the Amazon CloudWatch Logs streams and AWS CloudFormation stack events from the head node and compute nodes for the last 14 days, unless specified explicitly in the configuration or in the parameters for the `export-cluster-logs` command\. The time it takes for the command to complete depends on the number of nodes in the cluster and the number log streams available in CloudWatch Logs\. For more information about the available log streams, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md)\.

### Preserved logs<a name="troubleshooting-v3-get-logs-preserve"></a>

Starting from 3\.0\.0, AWS ParallelCluster preserves CloudWatch Logs by default when a cluster is deleted\. If you want to delete a cluster and preserve its logs, make sure that [`Monitoring`](Monitoring-v3.md) / [`Logs`](Monitoring-v3.md#yaml-Monitoring-Logs) / [`CloudWatch`](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch) / [`DeletionPolicy`](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch-DeletionPolicy) isn't set to `Delete` in the cluster configuration\. Otherwise, change the value for this field to `Retain` and run the `pcluster update-cluster` command\. Then, run `pcluster delete-cluster --cluster-name <cluster_name>` to delete the cluster yet retain the log group that’s stored in Amazon CloudWatch\.

### Terminated node logs<a name="troubleshooting-v3-get-logs-terminated-node"></a>

If compute nodes self terminate after launching and there aren't any compute node logs for it in CloudWatch, submit a job that triggers a cluster scaling action\. Wait for the instance to fail and retrieve the instance console log\.

Log in to the cluster head node and get the compute node `instance-id` from the `/var/log/parallelcluster/slurm_resume.log` file\.

Retrieve the instance console log with the following command\.

```
aws ec2 get-console-output --instance-id i-abcdef01234567890
```

The console output log can help you debug the root cause of a compute node failure when the compute node log isn't available\.

## Troubleshooting cluster deployment issues<a name="troubleshooting-v3-cluster-deployment"></a>

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
    "version": "3.2.0",
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
+ [Use the CLI to view logstreams](#troubleshooting-v3-cluster-deployment-cli-logstreams)
+ [Re\-create the failed cluster with `rollback-on-failure`](#troubleshooting-v3-cluster-deployment-cli-fail-rollback)

### View AWS CloudFormation events on `CREATE_FAILED`<a name="troubleshooting-v3-cluster-deployment-events"></a>

You can use the console or the AWS ParallelCluster CLI to view CloudFormation events on `CREATE_FAILED` errors to help find the root cause\.

**Topics**
+ [View events in the CloudFormation console](#troubleshooting-v3-cluster-deployment-cloudformation)
+ [Use the CLI to view and filter CloudFormation events on `CREATE_FAILED`](#troubleshooting-v3-cluster-deployment-cli-events)

#### View events in the CloudFormation console<a name="troubleshooting-v3-cluster-deployment-cloudformation"></a>

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

#### Use the CLI to view and filter CloudFormation events on `CREATE_FAILED`<a name="troubleshooting-v3-cluster-deployment-cli-events"></a>

To diagnose the cluster creation issue you can use the [`pcluster get-cluster-stack-events`](pcluster.get-cluster-stack-events-v3.md) command, by filtering for `CREATE_FAILED` status\. For more information, see [Filtering AWS CLI output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-filter.html) in the *AWS Command Line Interface User Guide*\.

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

### Use the CLI to view logstreams<a name="troubleshooting-v3-cluster-deployment-cli-logstreams"></a>

To debug this kind of issue, you can list the log streams available from the head node with the [`pcluster list-cluster-log-streams`](pcluster.list-cluster-log-streams-v3.md) by filtering for `node-type`, and then analyzing the log streams content\.

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

The two primary log streams that you can use to pinpoint initialization errors are the following:
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

In the previous example, the failure is due to a `runpostinstall` failure, so strictly related to the content of the custom bootstrap script used in the `OnNodeConfigured` configuration parameter of the [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions)\.

### Re\-create the failed cluster with `rollback-on-failure`<a name="troubleshooting-v3-cluster-deployment-cli-fail-rollback"></a>

AWS ParallelCluster creates cluster CloudWatch log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\. If there are no log streams available, the failure might be caused by the [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) custom bootstrap script or an AMIs related issue\. To diagnose the creation issue in this case, create the cluster again using [`pcluster create-cluster`](pcluster.create-cluster-v3.md), including the `--rollback-on-failure` parameter set to `false`\. Then, SSH into the cluster:

```
$ pcluster create-cluster --cluster-name mycluster --region eu-west-1 \
   --cluster-configuration cluster-config.yaml --rollback-on-failure false
 {
   "cluster": {
     "clusterName": "mycluster",
     "cloudformationStackStatus": "CREATE_IN_PROGRESS",
     "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f01-11ec-a3b9-024fcc6f3387",
     "region": "eu-west-1",
     "version": "3.2.0",
     "clusterStatus": "CREATE_IN_PROGRESS"
   }
 } 
 $ pcluster ssh --cluster-name mycluster
```

After you're logged into the head node, you should find three primary log files that you can use to pinpoint the error\.
+  `/var/log/cfn-init.log` is the log for the `cfn-init` script\. First check this log\. You're likely to see an error like `Command chef failed` in this log\. Look at the lines immediately before this line for more specifics connected with the error message\. For more information, see [cfn\-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html)\.
+  `/var/log/cloud-init.log` is the log for [cloud\-init](https://cloudinit.readthedocs.io/)\. If you don't see anything in `cfn-init.log`, then try checking this log next\. 
+  `/var/log/cloud-init-output.log` is the output of commands that were run by [cloud\-init](https://cloudinit.readthedocs.io/)\. This includes the output from `cfn-init`\. In most cases, you don't need to look at this log to troubleshoot this type of issue\.

## Troubleshooting scaling issues<a name="troubleshooting-v3-scaling-issues"></a>

This section is relevant to clusters that were installed using AWS ParallelCluster version 3\.0\.0 and later with the Slurm job scheduler\. For more information about configuring multiple queues, see [Configuration of Multiple Queues](configuration-of-multiple-queues-v3.md)\.

If one of your running clusters is experiencing issues, place the cluster in a `STOPPED` state by running the following command before you begin to troubleshoot\. This prevents incurring any unexpected costs\.

```
$ pcluster update-compute-fleet --cluster-name mycluster \
   --status STOP_REQUESTED
```

You can list the log streams available from the cluster nodes by using the [`pcluster list-cluster-log-streams`](pcluster.list-cluster-log-streams-v3.md) command and filtering using the `private-dns-name` of one of the failing nodes or the head node\.

```
$ pcluster list-cluster-log-streams --cluster-name mycluster --region eu-west-1 \
 --filters 'Name=private-dns-name,Values=ip-10-0-0-101'
```

Then, you can retrieve the content of the log stream to analyze it by using the [`pcluster get-cluster-log-events`](pcluster.get-cluster-log-events-v3.md) command and passing the `--log-stream-name` corresponding to one of the key logs mentioned in the following section\.

```
$ pcluster get-cluster-log-events --cluster-name mycluster \
--region eu-west-1 --log-stream-name ip-10-0-0-13.i-04e91cc1f4ea796fe.cfn-init
```

AWS ParallelCluster creates cluster CloudWatch log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

**Topics**
+ [Key logs for debugging](#troubleshooting-v3-key-logs)
+ [Troubleshooting node initialization issues](#troubleshooting-v3-node-init)
+ [**Troubleshooting unexpected node replacements and terminations**](#troubleshooting-unexpected-node-replacements-and-terminations)
+ [**Replacing, terminating, or powering down problematic instances and nodes**](#replacing-terminating-or-powering-down-problematic-instances-and-nodes)
+ [Queue \(partion\) `Inactive` status](#troubleshooting-v3-queues)
+ [Troubleshooting other known node and job issues](#troubleshooting-v3-other-node-job-issues)

### Key logs for debugging<a name="troubleshooting-v3-key-logs"></a>

The following table provides an overview of the key logs for the head node:
+  `/var/log/cfn-init.log` \- This is the AWS CloudFormation init log\. It contains all commands that were run when an instance is set up\. It's useful for troubleshooting initialization issues\.
+  `/var/log/chef-client.log` \- This is the Chef client log\. It contains all commands that were run through Chef/CINC\. It's useful for troubleshooting initialization issues\.
+  `/var/log/parallelcluster/slurm_resume.log` \- This is a `ResumeProgram` log\. It launches instances for dynamic nodes and is useful for troubleshooting dynamic nodes launch issues\.
+  `/var/log/parallelcluster/slurm_suspend.log` \- This is the `SuspendProgram` log\. It's called when instances are terminated for dynamic nodes, and useful for troubleshooting dynamic nodes termination issues\. When you check this log, you should also check the `clustermgtd` log\.
+  `/var/log/parallelcluster/clustermgtd` \- This is the `clustermgtd` log\. It runs as the centralized daemon that manages most cluster operation actions\. It's useful for troubleshooting any launch, termination, or cluster operation issue\.
+  `/var/log/slurmctld.log` \- This is the Slurm control daemon log\. AWS ParallelCluster doesn't make scaling decisions\. Rather, it only attempts to launch resources to satisfy the Slurm requirements\. It's useful for scaling and allocation issues, job\-related issues, and any scheduler\-related launch and termination issues\.

These are the key logs for the compute nodes:
+  `/var/log/cloud-init-output.log` \- This is the [cloud\-init](https://cloudinit.readthedocs.io/) log\. It contains all commands that were run when an instance is set up\. It's useful for troubleshooting initialization issues\.
+  `/var/log/parallelcluster/computemgtd` \- This is the `computemgtd` log\. It runs on each compute node to monitor the node in the rare event that `clustermgtd` daemon on the head node is offline\. It's useful for troubleshooting unexpected termination issues\.
+  `/var/log/slurmd.log` \- This is the Slurm compute daemon log\. It's useful for troubleshooting initialization and compute failure related issues\.

### Troubleshooting node initialization issues<a name="troubleshooting-v3-node-init"></a>

This section covers how you can troubleshoot node initialization issues\. This includes issues where the node fails to launch, power up, or join a cluster\.

**Topics**
+ [Head node](#troubleshooting-v3-node-init.head-node)
+ [Compute nodes](#troubleshooting-v3-node-init.compute-node)

#### Head node<a name="troubleshooting-v3-node-init.head-node"></a>

Applicable logs:
+  `/var/log/cfn-init.log` 
+  `/var/log/chef-client.log` 
+  `/var/log/parallelcluster/clustermgtd` 
+  `/var/log/parallelcluster/slurm_resume.log` 
+  `/var/log/slurmctld.log` 

Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` logs or corresponding log streams\. These logs contain all the actions that were run when the head node is set up\. Most errors that occur during setup should have error messages located in the `/var/log/chef-client.log` log\. If `OnNodeStart` or `OnNodeConfigured` scripts are specified in the configuration of the cluster, double check that the script runs successfully through log messages\.

When a cluster is created, the head node must wait for the compute nodes to join the cluster before it can join the cluster\. As such, if the compute nodes fail to join the cluster, then the head node also fails\. You can follow one of these sets of procedures, depending on the type of compute notes you use, to troubleshoot this type of issue:

#### Compute nodes<a name="troubleshooting-v3-node-init.compute-node"></a>
+ Applicable logs:
  + `/var/log/cloud-init-output.log`
  + `/var/log/slurmd.log`
+ If compute node is launched, first check `/var/log/cloud-init-output.log`, which should contain the setup logs similar to the `/var/log/chef-client.log` log on the head node\. Most errors that occur during setup should have error messages located at the `/var/log/cloud-init-output.log` log\. If pre\-install or post\-install scripts are specified in cluster configuration, check that they ran successfully\.
+ If you’re using a custom AMI with modification to Slurm configuration, then there might be a Slurm related error that prevents the compute node from joining the cluster\. For scheduler related errors, check the `/var/log/slurmd.log` log\.

**Dynamic compute nodes:**
+ Search the `ResumeProgram` log \(`/var/log/parallelcluster/slurm_resume.log`\) for your compute node name to see if `ResumeProgram` was ever called with the node\. \(If `ResumeProgram` wasn’t ever called, you can check the `slurmctld` log \(`/var/log/slurmctld.log`\) to determine if Slurm ever tried to call `ResumeProgram` with the node\)\.
+ Note that incorrect permissions for `ResumeProgram` might cause `ResumeProgram` to fail silently\. If you’re using a custom AMI with modification to `ResumeProgram` setup, check that the `ResumeProgram` is owned by the `slurm` user and has the `744` \(`rwxr--r--`\) permission\.
+ If `ResumeProgram` is called, check to see if an instance is launched for the node\. If no instance was launched, you can see an error message that describes the launch failure\.
+ If the instance is launched, then there might have been a problem during the setup process\. You should see the corresponding private IP address and instance ID from the `ResumeProgram` log\. Moreover, you can look at corresponding setup logs for the specific instance\. For more information about troubleshooting a setup error with a compute node, see the next section\.

 **Static compute nodes:** 
+ Check the `clustermgtd` \(`/var/log/parallelcluster/clustermgtd`\) log to see if instances were launched for the node\. If they weren’t launched, there should be clear error message detailing the launch failure\. 
+ If instance is launched, there's some issue during setup process\. You should see the corresponding private IP address and instance ID from the `ResumeProgram` log\. Moreover, you can look at the corresponding setup logs for the specific instance\. 

 **Compute nodes backed by Spot instances:** 
+ If it's the first time you use spot instances and the job remains in a PD \(Pending state\), double check the `/var/log/parallelcluster/slurm_resume.log` file\. You'll probably find an error like the following:

  ```
  2022-05-20 13:06:24,796 - [slurm_plugin.common:add_instances_for_nodes] - ERROR - Encountered exception when launching instances for nodes (x1) ['spot-dy-t2micro-2']: An error occurred (AuthFailure.ServiceLinkedRoleCreationNotPermitted) when calling the RunInstances operation: The provided credentials do not have permission to create the service-linked role for EC2 Spot Instances.
  ```

  When using Spot Instances, an `AWSServiceRoleForEC2Spot` service\-linked role must exist in your account\. To create this role in your account using the AWS CLI, run the following command:

  ```
  $ aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
  ```

  For more information, see [Working with Spot Instances](spot-v3.md) in the AWS ParallelCluster User Guide and [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.

### **Troubleshooting unexpected node replacements and terminations**<a name="troubleshooting-unexpected-node-replacements-and-terminations"></a>

This section continues to explore how you can troubleshoot node related issues, specifically when a node is replaced or terminated unexpectedly\.
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/slurmctld.log` \(head node\)
  + `/var/log/parallelcluster/computemgtd` \(compute node\)

 **Nodes replaced or terminated unexpectedly** 
+  Check in the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) to see if `clustermgtd` took the action to replace or terminate a node\. Note that `clustermgtd` handles all normal node maintenance action\.
+  If `clustermgtd` replaced or terminated the node, there should be a message detailing why this action was taken on the node\. If the reason is scheduler related \(for example, because the node is in `DOWN`\), check in `slurmctld` log for more information\. If the reason is Amazon EC2 related, there should be informative message detailing the Amazon EC2 related issue that required the replacement\. 
+  If `clustermgtd` didn’t terminate the node, first check if this was an expected termination by Amazon EC2, more specifically a spot termination\. `computemgtd`, running on a Compute node, can also take an action to terminate a node if `clustermgtd` is determined as unhealthy\. Check `computemgtd` log \(`/var/log/parallelcluster/computemgtd`\) to see if `computemgtd` terminated the node\.

 **Nodes failed ** 
+ Check in `slurmctld` log \(`/var/log/slurmctld.log`\) to see why a job or a node failed\. Note that jobs are automatically re\-queued if a node failed\.
+ If `slurm_resume` reports that node is launched and `clustermgtd`reports after several minutes that there’s no corresponding instance in Amazon EC2 for that node, the node might fail during setup\. To retrieve the log from a compute \(`/var/log/cloud-init-output.log`\), do the following steps:
  + Submit a job to let Slurm spin up a new node\.
  + After the node starts, enable termination protection using this command\.

    ```
    $ aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --disable-api-termination
    ```
  + Retrieve the console output from the node with this command\.

    ```
    $ aws ec2 get-console-output --instance-id i-1234567890abcdef0 --output text
    ```

### **Replacing, terminating, or powering down problematic instances and nodes**<a name="replacing-terminating-or-powering-down-problematic-instances-and-nodes"></a>
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/parallelcluster/slurm_suspend.log` \(head node\)
+ In most cases, `clustermgtd` handles all expected instance termination action\. Check in the `clustermgtd` log to see why it failed to replace or terminate a node\.
+ For dynamic nodes failing [`SlurmSettings` Properties](Scheduling-v3.md#Scheduling-v3-SlurmSettings.properties), check in the `SuspendProgram` log to see if `SuspendProgram` was called by `slurmctld` with the specific node as argument\. Note that `SuspendProgram` doesn’t actually perform any action\. Rather, it only logs when it’s called\. All instance termination and `NodeAddr` reset is done by `clustermgtd`\. Slurm puts nodes back into a `POWER_SAVING` state after `SuspendTimeout` automatically\.
+ If compute nodes are failing continuously due to bootstrap failures, verify if they are being launched with [Slurm cluster protected mode](slurm-protected-mode-v3.md) enabled\. If protected mode isn't enabled, modify the protected mode settings to enable protected mode\. Troubleshoot and fix the bootstrap script\.

### Queue \(partion\) `Inactive` status<a name="troubleshooting-v3-queues"></a>

If you run `sinfo` and the output shows queues with `AVAIL` status of `inact`, your cluster might have [Slurm cluster protected mode](slurm-protected-mode-v3.md) enabled and the queue has been set to the `INACTIVE` state for a pre\-defined period of time\.

### Troubleshooting other known node and job issues<a name="troubleshooting-v3-other-node-job-issues"></a>

Another type of known issue is that AWS ParallelCluster might fail to allocate jobs or make scaling decisions\. With this type of issue, AWS ParallelCluster only launches, terminates, or maintains resources according to Slurm instructions\. For these issues, check the `slurmctld` log to troubleshoot them\.

## Placement groups and instance launch issues<a name="troubleshooting-v3-placemment-groups"></a>

To get the lowest inter\-node latency, use a *placement group*\. A placement group guarantees that your instances are on the same networking backbone\. If there aren't enough instances available when a request is made, an `InsufficientInstanceCapacity` error is returned\. To reduce the possibility of receiving this error when using cluster placement groups, set the [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) / [`PlacementGroup`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Enabled`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Enabled) parameter to `false`\.

For additional control over capacity access, consider [launching instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.

For more information, see [Troubleshooting instance launch issues](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html) and [Placement groups roles and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#concepts-placement-groups) in the *Amazon EC2 User Guide for Linux Instances*\.

## Directories that cannot be replaced<a name="troubleshooting-v3-dirs-must-keep"></a>

The following directories are shared between the nodes and cannot be replaced\.
+  `/home` \- This includes the default user home folder \(`/home/ec2_user` on Amazon Linux, `/home/centos` on CentOS, and `/home/ubuntu` on Ubuntu\)\.
+  `/opt/intel` \- This includes Intel MPI, Intel Parallel Studio, and related files\.
+  `/opt/slurm` \- This includes Slurm Workload Manager and related files\. \(Conditional, only if `Scheduler: slurm`\.\) 

## Troubleshooting issues in NICE DCV<a name="troubleshooting-v3-nice-dcv"></a>

The logs for NICE DCV are written to files in the `/var/log/dcv/` directory\. Reviewing these logs can help to troubleshoot issues\.

The instance type should have at least 1\.7 gibibyte \(GiB\) of RAM to run NICE DCV\. Nano and micro instance types don't have enough memory to run NICE DCV\.

AWS ParallelCluster creates NICE DCV log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

## Troubleshooting issues in clusters with AWS Batch integration<a name="troubleshooting-v3-batch"></a>

This section is relevant to clusters with AWS Batch scheduler integration\.

**Topics**
+ [Head node issues](#troubleshooting-v3-batch-head-node)
+ [Compute issues](#troubleshooting-v3-batch-compute-nodes)
+ [Job failures](#troubleshooting-v3-batch-job-fail)
+ [Connect timeout on endpoint URL error](#troubleshooting-v3-batch-connect-timeout)

### Head node issues<a name="troubleshooting-v3-batch-head-node"></a>

Head node related setup issues can be troubleshooted in the same way as a Slurm cluster \(except for Slurm specific logs\)\. For more information about these issues, see [Head node](#troubleshooting-v3-node-init.head-node)\.

### Compute issues<a name="troubleshooting-v3-batch-compute-nodes"></a>

AWS Batch manages the scaling and compute aspects of your services\. If you encounter compute related issues, see the AWS Batch [troubleshooting](https://docs.aws.amazon.com/batch/latest/userguide/troubleshooting.html) documentation for help\.

### Job failures<a name="troubleshooting-v3-batch-job-fail"></a>

If a job fails, you can run the [`awsbout`](awsbatchcli.awsbout-v3.md) command to retrieve the job output\. You can also run the [`awsbstat`](awsbatchcli.awsbstat-v3.md) command to obtain a link to the job logs stored by Amazon CloudWatch\.

### Connect timeout on endpoint URL error<a name="troubleshooting-v3-batch-connect-timeout"></a>

If multi\-node parallel jobs fail with error: `Connect timeout on endpoint URL`:
+ In the `awsbout` output log, check that the job is multi\-node parallel from the output: `Detected 3/3 compute nodes. Waiting for all compute nodes to start.`
+ Verify whether the compute nodes subnet is public\.

Multi\-node parallel jobs don't support the use of public subnets when using AWS Batch in AWS ParallelCluster\. You must use a private subnet for your compute nodes and jobs\. For more information, see [Compute environment considerations](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html#mnp-ce) in the *AWS Batch User Guide*\. To configure a private subnet for your compute nodes, see [AWS ParallelCluster with AWS Batch scheduler](network-configuration-v3.md#network-configuration-v3-batch)\.

## Troubleshooting multi\-user integration with Active Directory<a name="troubleshooting-v3-multi-user"></a>

This section is relevant to clusters integrated with an Active Directory \(AD\)\.

If the Active Directory \(AD\) integration feature isn't working as expected the SSSD logs can provide useful diagnostic information\. These logs are located in `/var/log/sssd` on cluster nodes\. By default, they're also stored in a cluster’s Amazon CloudWatch log group\.

**Topics**
+ [AD specific troubleshooting](#troubleshooting-v3-multi-ad-specific)
+ [Enable debug mode](#troubleshooting-v3-multi-user-debug)
+ [How to move from LDAPS to LDAP](#troubleshooting-v3-multi-user-ldaps-ldap)
+ [How to disable LDAPS server certificate verification](#troubleshooting-v3-multi-user-ldaps-verify)
+ [How to log in with a SSH key rather than password](#troubleshooting-v3-multi-user-ssh-login)
+ [How to reset a user password](#troubleshooting-v3-multi-user-reset-passwd)
+ [How to verify the joined domain](#troubleshooting-v3-multi-user-domain-verify)
+ [How to troubleshoot issues with certificates](#troubleshooting-v3-multi-user-certificates)
+ [How to verify that the integration with AD is working](#troubleshooting-v3-multi-user-ad-verify)
+ [How to troubleshoot logging in to compute nodes](#troubleshooting-v3-multi-user-ad-compute-node-login)
+ [Known issues with SimCenter StarCCM\+ jobs in a multi\-user environment](#troubleshooting-v3-multi-user-ad-starccm)
+ [Known issues with username resolution](#troubleshooting-v3-multi-user-name-resolution)
+ [How to resolve home directory create issues](#troubleshooting-v3-multi-home-directory)

### AD specific troubleshooting<a name="troubleshooting-v3-multi-ad-specific"></a>

This section is relevant to troubleshooting specific to an AD type\.

#### Simple AD<a name="troubleshooting-v3-multi-user-simple-ad"></a>
+ The `DomainReadOnlyUser` value must match the Simple AD directory base search for users:

  `cn=ReadOnlyUser,cn=Users,dc=corp,dc=pcluster,dc=com`

  Note `cn` for `Users`\.
+ Default administrator user is `Administrator`\.
+ `Ldapsearch` requires Netbios name before the username\.

  `Ldapsearch` syntax must be as follows:

  ```
  $ ldapsearch -x -D "corp\\Administrator" -w "Password" -H ldap://192.0.2.103 \
     -b "cn=Users,dc=corp,dc=pcluster,dc=com"
  ```

#### AWS Managed Microsoft AD<a name="troubleshooting-v3-multi-users-ms-ad"></a>
+ The `DomainReadOnlyUser` value must match the AWS Managed Microsoft AD directory base search for users:

  `cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com`
+ Default administrator user is `Admin`\.
+ `Ldapsearch` syntax must be as follows:

  ```
  $ ldapsearch -x -D "Admin" -w "Password" -H ldap://192.0.2.103 \
     -b "ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
  ```

### Enable debug mode<a name="troubleshooting-v3-multi-user-debug"></a>

Debug logs from SSSD can be useful to troubleshoot issues\. To enable debug mode, you must update the cluster with the following changes made to the cluster configuration:

```
DirectoryService:
  AdditionalSssdConfigs:
    debug_level: "0x1ff"
```

### How to move from LDAPS to LDAP<a name="troubleshooting-v3-multi-user-ldaps-ldap"></a>

Moving from LDAPS \(LDAP with TLS/SSL\) to LDAP is discouraged because LDAP alone doesn't provide any encryption\. Nevertheless, it can be useful for testing purposes and troubleshooting\.

You can restore the cluster to its previous configuration by updating the cluster with the previous configuration definition\.

To move from LDAPS to LDAP, you must update the cluster with the following changes in the cluster configuration:

```
DirectoryService:
  LdapTlsReqCert: never
  AdditionalSssdConfigs:
    ldap_auth_disable_tls_never_use_in_production: True
```

### How to disable LDAPS server certificate verification<a name="troubleshooting-v3-multi-user-ldaps-verify"></a>

It can be useful to temporarily disable LDAPS server certificate verification on the head node, for testing or troubleshooting purposes\.

You can restore the cluster to its previous configuration by updating the cluster with the previous configuration definition\.

To disable the LDAPS server certificate verification, you must update the cluster with the following changes in the cluster configuration:

```
DirectoryService:
  LdapTlsReqCert: never
```

### How to log in with a SSH key rather than password<a name="troubleshooting-v3-multi-user-ssh-login"></a>

The SSH key is created in `/home/$user/.ssh/id_rsa` after the first time you log in with a password\. To log in with the SSH key, you must log in with your password, copy the SSH key locally, and then use it to SSH password\-less as usual:

```
$ ssh -i $LOCAL_PATH_TO_SSH_KEY $username@$head_node_ip
```

### How to reset a user password<a name="troubleshooting-v3-multi-user-reset-passwd"></a>

Run the following command with a user and role having write permission on the directory:

```
$ aws ds reset-user-password \
  --directory-id "d-abcdef01234567890" \
  --user-name "USER_NAME" \
  --new-password "NEW_PASSWORD" \
  --region "region-id"
```

For more information, see [Reset a user password](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups_reset_password.html) in the *AWS Directory Service Administration Guide*\.

### How to verify the joined domain<a name="troubleshooting-v3-multi-user-domain-verify"></a>

The following command must run from an instance that's joined to the domain, not the head node\.

```
$ realm list corp.pcluster.com \
  type: kerberos \
  realm-name: CORP.PCLUSTER.COM \
  domain-name: corp.pcluster.com \
  configured: kerberos-member \
  server-software: active-directory \
  client-software: sssd \
  required-package: oddjob \
  required-package: oddjob-mkhomedir \
  required-package: sssd \
  required-package: adcli \
  required-package: samba-common-tools \
  login-formats: %U \
  login-policy: allow-realm-logins
```

### How to troubleshoot issues with certificates<a name="troubleshooting-v3-multi-user-certificates"></a>

When LDAPS communication isn't working, it can be due to errors in the TLS communication, which in turn can be due to issues with certificates\.

**Notes about Certificates:**
+ The certificate specified in cluster config `LdapTlsCaCert` must be a bundle of PEM certificates containing the certificates for the whole certificate of authority \(CA\) chain that issued certificates for the domain controllers\.
+ A bundle of PEM certificates is a file made of the concatenation of PEM certificates\.
+ A certificate in PEM format \(typically used in Linux\) is equivalent to a certificate in Base64 DER format \(typically exported by Windows\)\.
+ If the certificate for domain controllers is issued by a subordinate CA, then the certificate bundle must contain the certificate of both the subordinate and root CA\.

**Troubleshooting verification steps:**

The following verification steps assume that the commands are run from within the cluster head node and that the domain controller is reachable at `SERVER:PORT`\.

To troubleshoot an issue that's related to certificates, follow these verification steps:

**Verification steps:**

1. **Check the connection to the AD domain controllers:**

   Verify that you can connect to a domain controller\. If this steps succeeds, then the SSL connection to the domain controller succeeds and the certificate is verified\. Your issue isn't related to certificates\.

   If this step fails, go ahead with next verification\.

   ```
   $ openssl s_client -connect SERVER:PORT -CAfile PATH_TO_CA_BUNDLE_CERTIFICATE
   ```

1. **Check the certificate verification:**

   Verify that the local CA certificate bundle can validate the certificate provided by the domain controller\. If this step succeeds, then your issue isn't related to certificates, but to other networking issues\.

   If this step fails, go ahead with next verification\.

   ```
   $ openssl verify -verbose -CAfile PATH_TO_CA_BUNDLE_CERTIFICATE PATH_TO_A_SERVER_CERTIFICATE
   ```

1. **Check the certificate provided by the AD domain controllers:**

   Verify that the content of the certificate provided by the domain controllers is as expected\. If this step succeeds, you probably have issues with the CA certificate used to verify controllers, go to the next troubleshooting step\.

   If this step fails, you must correct the certificate issued for the domain controllers and re\-execute the troubleshooting steps\.

   ```
   $ openssl s_client -connect SERVER:PORT -showcerts
   ```

1. **Check the content of a certificate:**

   Verify that the content of the certificate that's provided by the domain controllers is as expected\. If this step succeeds, you probably have issues with the CA certificate used to verify controller’s, go to the next troubleshooting step\.

   If this step fails, you must correct the certificate issued for the domain controllers and rerun the troubleshooting steps\.

   ```
   $ openssl s_client -connect SERVER:PORT -showcerts
   ```

1. **Check the content of the local CA certificate bundle:**

   Verify that the content of the local CA certificate bundle used to validate domain controllers certificate is as expected\. If this step succeeds, you probably have issues with the certificate that are provided by the domain controllers\.

   If this step fails, you must correct CA certificate bundle issued for the domain controllers and rerun the troubleshooting steps\.

   ```
   $ openssl x509 -in PATH_TO_A_CERTIFICATE -text
   ```

### How to verify that the integration with AD is working<a name="troubleshooting-v3-multi-user-ad-verify"></a>

If the following two checks succeed, the integration with the Active Directory \(AD\) is working\.

**Checks:**

1. **You can discover users defined in the directory:**

   From within the cluster head node, as an `ec2-user`:

   ```
   $ getent passwd $ANY_AD_USER
   ```

1. **You can SSH into the head node providing the user password:**

   ```
   $ ssh $ANY_AD_USER@$HEAD_NODE_IP
   ```

If check one fails, we expect check two to fail also\.

Additional troubleshooting checks:
+ Verify that the user exists in the directory\.
+ Enable [debug logging](#troubleshooting-v3-multi-user-debug)\.
+ Consider temporarily disabling encryption by [moving from LDAPS to LDAP](#troubleshooting-v3-multi-user-ldaps-ldap) to rule out LDAPS issues\.

### How to troubleshoot logging in to compute nodes<a name="troubleshooting-v3-multi-user-ad-compute-node-login"></a>

This section is relevant to logging in to compute nodes in clusters integrated with AD\.

With AWS ParallelCluster, password logins to cluster compute nodes are disabled by design\.

All users must use their own SSH key to log in to compute nodes\.

Users can retrieve their SSH key in the head node after first authentication \(for example login\), if [`GenerateSshKeysForUsers`](DirectoryService-v3.md#yaml-DirectoryService-GenerateSshKeysForUsers) is enabled in the cluster configuration\.

When users authenticate on the head node for the first time, they can retrieve SSH keys that are automatically generated for them as directory users\. Home directories for the user are also created\. This can also happen the first time a sudo\-user switches to a user in the head node\.

If a user hasn't logged into the head node, SSH keys aren't generated and the user won't be able to log in to compute nodes\.

### Known issues with SimCenter StarCCM\+ jobs in a multi\-user environment<a name="troubleshooting-v3-multi-user-ad-starccm"></a>

This section is relevant to jobs launched in a multi\-user environment by Simcenter StarCCM\+ computational fluid dynamics software from Siemens\.

If you run StarCCM\+ v16 jobs configured to use the embedded IntelMPI, by default the MPI processes are bootstrapped using SSH\.

Due to a known [Slurm bug](https://bugs.schedmd.com/show_bug.cgi?id=13385) that causes username resolution to be wrong, jobs might fail with an error like `error setting up the bootstrap proxies`\. This bug only impacts AWS ParallelCluster versions 3\.1\.1 and 3\.1\.2\.

To prevent this from occurring, force IntelMPI to use Slurm as MPI boostrap method\. Export the environment variable `I_MPI_HYDRA_BOOTSTRAP=slurm` into the job script that launches StarCCM\+, as described in the [IntelMPI official documentation](https://www.intel.com/content/www/us/en/develop/documentation/mpi-developer-reference-linux/top/environment-variable-reference/hydra-environment-variables.html)\.

### Known issues with username resolution<a name="troubleshooting-v3-multi-user-name-resolution"></a>

This section is relevant to retrieving usernames within jobs\.

Due to a known [bug in Slurm](https://bugs.schedmd.com/show_bug.cgi?id=13385), the username retrieved within a job process might be `nobody` if you run a job without `srun`\. This bug only impacts AWS ParallelCluster versions 3\.1\.1 and 3\.1\.2\.

For example, if you run the command `sbatch --wrap 'srun id'` as a directory user, the correct username is returned\. However, if you run the `sbatch --wrap 'id'` as a directory user, `nobody` might be returned as the username\.

You can use the following workarounds\.

1. Launch your job with `'srun'` instead of `'sbatch'`, if possible\.

1. Enable SSSD enumeration by setting the [AdditionalSssdConfigs](DirectoryService-v3.md#yaml-DirectoryService-AdditionalSssdConfigs) in cluster configuration as follows\.

   ```
   AdditionalSssdConfigs:
     enumerate: true
   ```

### How to resolve home directory create issues<a name="troubleshooting-v3-multi-home-directory"></a>

This section is relevant to home directory creation issues\.

If you see errors like the one shown in the following example, a home directory wasn't created for you when you first logged in to the head node\. Or, a home directory wasn't created for you when you first switched from a sudoer to an AD user in the head node\.

```
$ ssh AD_USER@$HEAD_NODE_IP
/opt/parallelcluster/scripts/generate_ssh_key.sh failed: exit code 1

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
Could not chdir to home directory /home/PclusterUser85: No such file or directory
```

The home directory create failure can be caused by the `oddjob` and `oddjob-mkhomedir` packages installed in the cluster head node\.

Without a home directory and SSH key, the user can't submit jobs or SSH into the cluster nodes\.

If you need the `oddjob` packages in your system, verify that the `oddjobd` service is running and refresh the PAM config files to make sure that the home directory is created\. To do this, run the commands in the head node as shown in the following example\.

```
sudo systemctl start oddjobd
sudo authconfig --enablemkhomedir --updateall
```

If you don't need the `oddjob` packages in your system, uninstall them and refresh the PAM config files to make sure that the home directory is created\. To do this, run the commands in the head node as shown in the following example\.

```
sudo yum remove -y oddjob oddjob-mkhomedir
sudo authconfig --enablemkhomedir --updateall
```

## Troubleshooting custom AMI issues<a name="troubleshooting-v3-custom-amis"></a>

When you use a custom AMI, you can see the following warnings:

```
"validationMessages": [
  {
    "level": "WARNING",
    "type": "CustomAmiTagValidator",
    "message": "The custom AMI may not have been created by pcluster. You can ignore this warning if the AMI is shared or copied from another pcluster AMI. If the AMI is indeed not created by pcluster, cluster creation will fail. If the cluster creation fails, please go to https://docs.aws.amazon.com/parallelcluster/latest/ug/troubleshooting.html#troubleshooting-stack-creation-failures for troubleshooting."
  },
  {
   "level": "WARNING",
   "type": "AmiOsCompatibleValidator",
   "message": "Could not check node AMI ami-0000012345 OS and cluster OS alinux2 compatibility, please make sure they are compatible before cluster creation and update operations."
  }
]
```

If you're sure that the correct AMI is being used, you can ignore these warnings\.

If you don't want to see these warnings in the future, tag the custom AMI with the following tags, where *`my-os`* is one of `alinux2`, `ubuntu1804`, `ubuntu2004`, or `centos7` and *"3\.2\.0"* is the `pcluster` version in use:

```
$ aws ec2 create-tags \
  --resources ami-yourcustomAmi \
  --tags Key="parallelcluster:version",Value="3.2.0" Key="parallelcluster:os",Value="my-os"
```

## Troubleshooting a cluster update timeout when `cfn-hup` isn't running<a name="troubleshooting-v3-cluster-update-timeout"></a>

The `cfn-hup` helper is a daemon that detects changes in resource metadata and runs user\-specified actions when a change is detected\. This is how you make configuration updates on your running Amazon EC2 instances through the `UpdateStack` API action\.

Currently the `cfn-hup` daemon is launched by the `supervisord`\. But after launch, the `cfn-hup` process is detached from `supervisord` control\. If the `cfn-hup` demon is killed by an external actor, it's not restarted automatically\. If `cfn-hup` isn't running, during a cluster update, the CloudFormation stack starts the update process as expected but the update procedure isn't activated on the head node and the stack eventually goes into timeout\. From the cluster logs `/var/log/chef-client`, you can see that the update recipe is never invoked\.

**Check and restart `cfn-hup` in case of failures**

1. On the headnode, check if `cfn-hup` is running:

   ```
   $ ps aux | grep cfn-hup
   ```

1. Check `cfn-hup` log `/var/log/cfn-hup.log` and `/var/log/supervisord.log` on the head node\.

1. If `cfn-hup` isn't running, try restarting it by running:

   ```
   $ sudo /opt/parallelcluster/pyenv/versions/cookbook_virtualenv/bin/supervisorctl start cfn-hup
   ```

## Network troubleshooting<a name="troubleshooting-v3-networking"></a>

### Cluster in a single public subnet issues<a name="troubleshooting-v3-networking-private-subnet"></a>

Check the `cloud-init-output.log` from one of the compute nodes\. If you find something like the following that indicates the node is stuck in slurm initialization, it is most likely due to a missing DynamoDB VPC endpoint\. You must add the DynamoDB endpoint\. For more information see [AWS ParallelCluster in a single subnet with no internet access](network-configuration-v3.md#aws-parallelcluster-in-a-single-public-subnet-no-internet-v3)\.

```
ruby_block[retrieve compute node info] action run[2022-03-11T17:47:11+00:00] INFO: Processing ruby_block[retrieve compute node info] action run (aws-parallelcluster-slurm::init line 31)
```

## Additional support<a name="troubleshooting-v3-additional-support"></a>

For a list of known issues, see the main [GitHub Wiki](https://github.com/aws/aws-parallelcluster/wiki)\.

page or the [issues](https://github.com/aws/aws-parallelcluster/issues) page\. For more urgent issues, contact AWS Support or open a [new GitHub issue](https://github.com/aws/aws-parallelcluster/issues)\.