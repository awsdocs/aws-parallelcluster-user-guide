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
+ [Additional support](#troubleshooting-v3-additional-support)

## Retrieving and preserving logs<a name="troubleshooting-v3-get-logs"></a>

EC2 metrics for Head Node and storage, and Head Node log streams are available in the CloudWatch Dashboard created within the cluster: [Amazon CloudWatch dashboard](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch)\. 

Logs are a useful resource for troubleshooting issues\. If you want to delete a failing cluster, you should first create an archive of cluster logs\. Follow the following steps to start this process\.

It is possible to archive the logs in S3 or in a local file \(depending on the `--output-file` parameter\)\.

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

The archive contains the Amazon CloudWatch Logs streams and AWS CloudFormation stack events from the Head Node and Compute Nodes for the last 14 days, unless specified explicitly in the configuration or in the parameters for the `export-cluster-logs` command\. Please note take the command execution may to run depending on the number of nodes in the cluster and log streams available in CloudWatch Logs\. For more information about the available log streams, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs.md)\.

Starting from 3\.0\.0, AWS ParallelCluster preserves CloudWatch Logs by default when a cluster is deleted\. If you want to delete a cluster and preserve its logs, make sure that `Monitoring > Logs > CloudWatch > DeletionPolicy` isn't set to `Delete` in the cluster configuration\. Otherwise, change the value for this field to `Retain` and run the `pcluster update-cluster` command\. Then, run `pcluster delete-cluster --cluster-name <cluster_name>` to delete the cluster yet retain the log group that’s stored in Amazon CloudWatch\.

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
    "version": "3.1.1",
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

To diagnose the cluster creation issue you can use the ``pcluster get-cluster-stack-events`` command, by filtering for `CREATE_FAILED` status\. For more information, see [Filtering AWS CLI output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-filter.html) in the *AWS Command Line Interface User Guide*\.

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

In the previous example, the failure was in the `HeadNode` setup\. To debug this kind of issue, you can list the log streams available from the head node with the ``pcluster list-cluster-log-streams`` by filtering for `node-type`, and then analyzing the log streams content\.

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

You can retrieve the content of the log stream by using the ``pcluster get-cluster-log-events`` \(note the `--limit 5` option to limit the number of retrieved events\):

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

In the previous example, the failure is due to a `runpostinstall` failure, so strictly related to the content of the custom bootstrap script used in the `OnNodeConfigured` configuration parameter of the ``CustomActions``\.

The head node log streams are available in the CloudWatch Dashboard created within the cluster: [Amazon CloudWatch dashboard](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch)\. If there are no log streams available, the failure might be caused by the ``CustomActions`` custom bootstrap script or an AMIs related issue\. To diagnose the creation issue in this case, create the cluster again using ``pcluster create-cluster``, including the `--rollback-on-failure` parameter set to `false`\. Then, SSH into the cluster:

```
$ pcluster create-cluster --cluster-name mycluster --region eu-west-1 \
  --cluster-configuration cluster-config.yaml --rollback-on-failure false
{
  "cluster": {
    "clusterName": "mycluster",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:xxx:stack/mycluster/1bf6e7c0-0f01-11ec-a3b9-024fcc6f3387",
    "region": "eu-west-1",
    "version": "3.1.1",
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

You can list the log streams available from the cluster nodes by using the ``pcluster list-cluster-log-streams`` command and filtering using the `private-dns-name` of one of the failing nodes or the head node\.

```
$ pcluster list-cluster-log-streams --cluster-name mycluster --region eu-west-1 \
 --filters 'Name=private-dns-name,Values=ip-10-0-0-101'
```

Then, you can retrieve the content of the log stream to analyze it by using the ``pcluster get-cluster-log-events`` command and passing the `--log-stream-name` corresponding to one of the key logs mentioned in the following section\.

```
$ pcluster get-cluster-log-events --cluster-name mycluster \
--region eu-west-1 --log-stream-name ip-10-0-0-13.i-04e91cc1f4ea796fe.cfn-init
```

Note: The head and compute node log streams are also available in the CloudWatch Dashboard that was created within the cluster: [Amazon CloudWatch dashboard](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch)\. 

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
    aws ec2 modify-instance-attribute --instance-id i-xyz --disable-api-termination
    ```
  + Retrieve the console output from the node with this command\.

    ```
    aws ec2 get-console-output --instance-id i-xyz --output text
    ```

### **Replacing, terminating, or powering down problematic instances and nodes**<a name="replacing-terminating-or-powering-down-problematic-instances-and-nodes"></a>
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/parallelcluster/slurm_suspend.log` \(head node\)
+ In most cases, `clustermgtd` handles all expected instance termination action\. Check in the `clustermgtd` log to see why it failed to replace or terminate a node\.
+ For dynamic nodes failing ``SlurmSettings` Properties`, check in the `SuspendProgram` log to see if `SuspendProgram` was called by `slurmctld` with the specific node as argument\. Note that `SuspendProgram` doesn’t actually perform any action\. Rather, it only logs when it’s called\. All instance termination and `NodeAddr` reset is done by `clustermgtd`\. Slurm puts nodes back into a `POWER_SAVING` state after `SuspendTimeout` automatically\.

### Troubleshooting other known node and job issues<a name="troubleshooting-v3-other-node-job-issues"></a>

Another type of known issue is that AWS ParallelCluster might fail to allocate jobs or make scaling decisions\. With this type of issue, AWS ParallelCluster only launches, terminates, or maintains resources according to Slurm instructions\. For these issues, check the `slurmctld` log to troubleshoot them\.

## Placement groups and instance launch issues<a name="troubleshooting-v3-placemment-groups"></a>

To get the lowest inter\-node latency, use a *placement group*\. A placement group guarantees that your instances are on the same networking backbone\. If there aren't enough instances available when a request is made, an `InsufficientInstanceCapacity` error is returned\. To reduce the possibility of receiving this error when using cluster placement groups, set the ``Networking` Properties` parameter to `true`\.

If you require a high performance shared filesystem, consider using [Amazon FSx for Lustre](http://aws.amazon.com/fsx/lustre/)\.

For more information, see [Troubleshooting instance launch issues](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html) and [Placement groups roles and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#concepts-placement-groups) in the *Amazon EC2 User Guide for Linux Instances* 

## Directories that cannot be replaced<a name="troubleshooting-v3-dirs-must-keep"></a>

The following directories are shared between the nodes and cannot be replaced\.
+  `/home` \- This includes the default user home folder \(`/home/ec2_user` on Amazon Linux, `/home/centos` on CentOS, and `/home/ubuntu` on Ubuntu\)\.
+  `/opt/intel` \- This includes Intel MPI, Intel Parallel Studio, and related files\.
+  `/opt/slurm` \- This includes Slurm Workload Manager and related files\. \(Conditional, only if `Scheduler: slurm`\.\) 

## Troubleshooting issues in NICE DCV<a name="troubleshooting-v3-nice-dcv"></a>

The logs for NICE DCV are written to files in the `/var/log/dcv/` directory\. Reviewing these logs can help to troubleshoot issues\.

The instance type should have at least 1\.7 gibibyte \(GiB\) of RAM to run NICE DCV\. Nano and micro instance types don't have enough memory to run NICE DCV\.

Note: NICE DCV log streams are available in the Dashboard created within the cluster: [Amazon CloudWatch dashboard](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch) 

## Troubleshooting issues in clusters with AWS Batch integration<a name="troubleshooting-v3-batch"></a>

This section is relevant to clusters with AWS Batch scheduler integration\.

### Head node issues<a name="troubleshooting-v3-batch-head-node"></a>

Head node related setup issues can be troubleshooted in the same way as a Slurm cluster \(except for Slurm specific logs\)\. For more information about these issues, see [Head node](#troubleshooting-v3-node-init.head-node)\.

### Compute issues<a name="troubleshooting-v3-batch-compute-nodes"></a>

AWS Batch manages the scaling and compute aspects of your services\. If you encounter compute related issues, see the AWS Batch [troubleshooting](https://docs.aws.amazon.com/batch/latest/userguide/troubleshooting.html) documentation for help\.

### Job failures<a name="troubleshooting-v3-batch-job-fail"></a>

If a job fails, you can run the [`awsbout`](awsbatchcli_awsbout.md) command to retrieve the job output\. You can also run the [`awsbstat`](awsbatchcli.awsbstat-v3.md) command to obtain a link to the job logs stored by Amazon CloudWatch\.

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
+ [Known issues with StarCCM\+ jobs in a multi\-user environment](#troubleshooting-v3-multi-user-ad-starccm)
+ [Known issues with user name resolution](#troubleshooting-v3-multi-user-name-resolution)

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

Users can retrieve their SSH key in the head node after first login, if [`GenerateSshKeysForUsers`](DirectoryService-v3.md#yaml-DirectoryService-GenerateSshKeysForUsers) is enabled in the cluster configuration\.

When users log in to the head node for the first time, they can retrieve SSH keys that are automatically generated for them as directory users\. Home directories for the user are also created\. This can also happen the first time a sudo\-user switches to a user in the head node\.

If a user hasn't logged into the head node, SSH keys aren't generated and the user won't be able to log in to compute nodes\.

### Known issues with StarCCM\+ jobs in a multi\-user environment<a name="troubleshooting-v3-multi-user-ad-starccm"></a>

This section is relevant to StarCCM\+ jobs in a multi\-user environment\.

If you run StarCCM\+ v16 jobs configured to use the embedded IntelMPI, by default the MPI processes are bootstrapped using SSH\.

Due to a known [Slurm bug](https://bugs.schedmd.com/show_bug.cgi?id=13385) that causes username resolution to be wrong, jobs might fail with an error message like "`error setting up the bootstrap proxies`"\.

To prevent this from occurring, force IntelMPI to use Slurm as MPI bootstrap method\. Export the environment variable `I_MPI_HYDRA_BOOTSTRAP=slurm` into the job script that launches StarCCM\+, as described in the [IntelMPI official documentation](https://www.intel.com/content/www/us/en/develop/documentation/mpi-developer-reference-linux/top/environment-variable-reference/hydra-environment-variables.html)\.

### Known issues with username resolution<a name="troubleshooting-v3-multi-user-name-resolution"></a>

This section is relevant to retrieving usernames within jobs\.

Due to a known [bug in Slurm](https://bugs.schedmd.com/show_bug.cgi?id=13385), the username retrieved within a job process might be `nobody` if you run a job without `srun`\.

For example, if you run the command `sbatch --wrap 'srun id'` as a directory user, the correct username is returned\. However, if you run the `sbatch --wrap 'id'` as a directory user, `nobody` might be returned as username\.
For example, if you run the command `sbatch --wrap 'srun id'` as a directory user, the correct username is returned\. However, if you run the `sbatch --wrap 'id'` as a directory user, `nobody` might be returned as username\.

You can use the following workarounds\.

1. Launch your job with `srun` instead of `sbatch`, if possible\.

1. Enable SSSD enumeration by setting the [AdditionalSssdConfigs](DirectoryService-v3.md#yaml-DirectoryService-AdditionalSssdConfigs) in cluster configuration as follows\.

   ```
   AdditionalSssdConfigs:
     enumerate: true
   ```
Depending on the size of the directory, enabling enumeration might impact the job run time performance.

## Additional support<a name="troubleshooting-v3-additional-support"></a>

For a list of known issues, see the main [GitHub Wiki](https://github.com/aws/aws-parallelcluster/wiki) 

page or the [issues](https://github.com/aws/aws-parallelcluster/issues) page\. For more urgent issues, contact AWS Support or open a [new GitHub issue](https://github.com/aws/aws-parallelcluster/issues)\.
