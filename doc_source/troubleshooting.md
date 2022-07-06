# AWS ParallelCluster troubleshooting<a name="troubleshooting"></a>

**Topics**
+ [Retrieving and preserving logs](#retrieving-and-preserve-logs)
+ [Troubleshooting stack deployment issues](#troubleshooting-stack-creation-failures)
+ [Troubleshooting issues in multiple queue mode clusters](#multiple-queue-mode)
+ [Troubleshooting issues in single queue mode clusters](#troubleshooting-issues-in-single-queue-clusters)
+ [Placement groups and instance launch issues](#placement-groups-and-instance-launch-issues)
+ [Directories that cannot be replaced](#directories-cannot-be-replaced)
+ [Troubleshooting issues in NICE DCV](#nice-dcv-troubleshooting)
+ [Troubleshooting issues in clusters with AWS Batch integration](#clusters-with-aws-batch-integration)
+ [Troubleshooting when a resource fails to create](#troubleshooting-resource-fails-to-create)
+ [Troubleshooting IAM policy size issues](#troubleshooting-policy-size-issues)
+ [Additional support](#getting-support)

The AWS ParallelCluster community maintains a Wiki page that provides many troubleshooting tips on the [AWS ParallelCluster GitHub Wiki](https://github.com/aws/aws-parallelcluster/wiki/)\. For a list of known issues, see [Known issues](https://github.com/aws/aws-parallelcluster/wiki#known-issues-)\.

## Retrieving and preserving logs<a name="retrieving-and-preserve-logs"></a>

 Logs are a useful resource for troubleshooting issues\. Before you can use logs to troubleshoot issues for your AWS ParallelCluster resources, you should first create an archive of cluster logs\. Follow the steps described in the [Creating an Archive of a Cluster’s Logs](https://github.com/aws/aws-parallelcluster/wiki/Creating-an-Archive-of-a-Cluster's-Logs) topic on the [AWS ParallelCluster GitHub Wiki](https://github.com/aws/aws-parallelcluster/wiki/) to start this process\.

If one of your running clusters is experiencing issues, you should place the cluster in a `STOPPED` state by running the ``pcluster stop` <cluster_name>` command before you begin to troubleshoot\. This prevents incurring any unexpected costs\.

 If `pcluster` stops functioning or if you want to delete a cluster while still preserving its logs, run the ``pcluster delete` —keep-logs <cluster_name>` command\. Running this command deletes the cluster yet retains the log group that’s stored in Amazon CloudWatch\. For more information about this command, see the [`pcluster delete`](pcluster.delete.md) documentation\.

## Troubleshooting stack deployment issues<a name="troubleshooting-stack-creation-failures"></a>

If your cluster fails to be created and rolls back stack creation, you can look through the following log files to diagose the issue\. You want to look for the output of `ROLLBACK_IN_PROGRESS` in these logs\. The failure message should look like the following:

```
$ pcluster create mycluster
Creating stack named: parallelcluster-mycluster
Status: parallelcluster-mycluster - ROLLBACK_IN_PROGRESS                        
Cluster creation failed.  Failed events:
  - AWS::EC2::Instance MasterServer Received FAILURE signal with UniqueId i-07af1cb218dd6a081
```

To diagnose the issue, create the cluster again using [`pcluster create`](pluster.create.md), including the `--norollback` flag\. Then, SSH into the cluster:

```
$ pcluster create mycluster --norollback
...
$ pcluster ssh mycluster
```

After you're logged into the head node, you should find three primary log files that you can use to pinpoint the error\.
+ `/var/log/cfn-init.log` is the log for the `cfn-init` script\. First check this log\. You're likely to see an error like `Command chef failed` in this log\. Look at the lines immediately before this line for more specifics connected with the error message\. For more information, see [cfn\-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html)\.
+ `/var/log/cloud-init.log` is the log for [cloud\-init](https://cloudinit.readthedocs.io/)\. If you don't see anything in `cfn-init.log`, then try checking this log next\.
+ `/var/log/cloud-init-output.log` is the output of commands that were run by [cloud\-init](https://cloudinit.readthedocs.io/)\. This includes the output from `cfn-init`\. In most cases, you don't need to look at this log to troubleshoot this type of issue\.

## Troubleshooting issues in multiple queue mode clusters<a name="multiple-queue-mode"></a>

 This section is relevant to clusters that were installed using AWS ParallelCluster version 2\.9\.0 and later with the Slurm job scheduler\. For more information about multiple queue mode, see [Multiple queue mode](queue-mode.md)\.

### Key logs<a name="key-logs"></a>

 The following table provides an overview of the key logs for the head node:

`/var/log/cfn-init.log`  
This is the AWS CloudFormation init log\. It contains all commands that were run when an instance is set up\. It's useful for troubleshooting initialization issues\.

`/var/log/chef-client.log`  
This is the Chef client log\. It contains all commands that were run through Chef/CINC\. It's useful for troubleshooting initialization issues\.

`/var/log/parallelcluster/slurm_resume.log`  
This is a `ResumeProgram` log\. It launches instances for dynamic nodes and useful for troubleshooting dynamic nodes launch issues\.

`/var/log/parallelcluster/slurm_suspend.log`  
This is the `SuspendProgram` log\. It's called when instances are terminated for dynamic nodes, and useful for troubleshooting dynamic nodes termination issues\. When you check this log, you should also check the `clustermgtd` log\.

`/var/log/parallelcluster/clustermgtd`  
This is the `clustermgtd` log\. It runs as the centralized daemon that manages most cluster operation actions\. It's useful for troubleshooting any launch, termination, or cluster operation issue\.

`/var/log/slurmctld.log`  
This is the Slurm control daemon log\. AWS ParallelCluster doesn't make scaling decisions\. Rather, it only attempts to launch resources to satisfy the Slurm requirements\. It's useful for scaling and allocation issues, job\-related issues, and any scheduler\-related launch and termination issues\.

These are the Key notes for the Compute nodes:

`/var/log/cloud-init-output.log`  
This is the [cloud\-init](https://cloudinit.readthedocs.io/) log\. It contains all commands that were run when an instance is set up\. It's useful for troubleshooting initialization issues\.

`/var/log/parallelcluster/computemgtd`  
This is the `computemgtd` log\. It runs on each compute node to monitor the node in the rare event that `clustermgtd` daemon on the head node is offline\. It's useful for troubleshooting unexpected termination issues\. 

`/var/log/slurmd.log`  
This is the Slurm compute daemon log\. It's useful for troubleshooting initialization and compute failure related issues\.

### **Troubleshooting node initialization issues**<a name="troubleshooting-node-initialization-issues"></a>

This section covers how you can troubleshoot node initialization issues\. This includes issues where the node fails to launch, power up, or join a cluster\.

**Head node:**

Applicable logs:
+ `/var/log/cfn-init.log`
+ `/var/log/chef-client.log`
+ `/var/log/parallelcluster/clustermgtd`
+ `/var/log/parallelcluster/slurm_resume.log`
+ `/var/log/slurmctld.log`

Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` logs\. These logs should contain all the actions that were run when the head node is set up\. Most errors that occur during setup should have a error messages located in the `/var/log/chef-client.log` log\. If pre\-install or post\-install scripts are specified in the configuration of the cluster, double check that the script runs successfully through log messages\.

When a cluster is created, the head node needs to wait for the compute nodes to join the cluster before it can join the cluster\. As such, if the compute nodes fail to join the cluster, then the head node also fails\. You can follow one of these sets of procedures, depending on the type of compute notes you use, to troubleshoot this type of issue:

**Dynamic compute nodes:**
+ Search the `ResumeProgram` log \(`/var/log/parallelcluster/slurm_resume.log`\) for your compute node name to see if `ResumeProgram` was ever called with the node\. \(If `ResumeProgram` wasn’t ever called, you can check the `slurmctld` log \(`/var/log/slurmctld.log`\) to determine if Slurm ever tried to call `ResumeProgram` with the node\.\)
+ Note that incorrect permissions for `ResumeProgram` might cause `ResumeProgram` to fail silently\. If you’re using a custom AMI with modification to `ResumeProgram` setup, check that the `ResumeProgram` is owned by the `slurm` user and has the `744` \(`rwxr--r--`\) permission\.
+ If `ResumeProgram` is called, check to see if an instance is launched for the node\. If no instance was launched, you should be able to see an error message that describes the launch failure\.
+ If the instance is launched, then there might have been a problem during the setup process\. You should see the corresponding private IP address and instance ID from the `ResumeProgram` log\. Moreover, you can look at corresponding setup logs for the specific instance\. For more information about troubleshooting a setup error with a compute node, see the next section\.

 **Static compute nodes:** 
+ Check the `clustermgtd` \(`/var/log/parallelcluster/clustermgtd`\) log to see if instances were launched for the node\. If they weren’t launched, there should be clear error message detailing the launch failure\.
+ If instance is launched, there's some issue during setup process\. You should see the corresponding private IP address and instance ID from the `ResumeProgram` log\. Moreover, you can look at the corresponding setup logs for the specific instance\. 
+ **Compute nodes:**
  + **Applicable logs:**
    + `/var/log/cloud-init-output.log`
    + `/var/log/slurmd.log`
  + If compute node is launched, first check `/var/log/cloud-init-output.log`, which should contain the setup logs similar to the `/var/log/chef-client.log` log on the head node\. Most errors that occur during setup should have error messages located at the `/var/log/cloud-init-output.log` log\. If pre\-install or post\-install scripts are specified in cluster configuration, check that they ran successfully\.
  + If you’re using a custom AMI with modification to Slurm configuration, then there might be a Slurm related error that prevents the compute node from joining the cluster\. For scheduler related errors, check the `/var/log/slurmd.log` log\.

### **Troubleshooting unexpected node replacements and terminations**<a name="troubleshooting-unexpected-node-replacements-and-terminations"></a>

This section continues to explore how you can troubleshoot node related issues, specifically when a node is replaced or terminated unexpectedly\.
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/slurmctld.log` \(head node\)
  + `/var/log/parallelcluster/computemgtd` \(compute node\)
+  **Nodes replaced or terminated unexpectedly** 
  +  Check in the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) to see if `clustermgtd` took the action to replace or terminate a node\. Note that `clustermgtd` handles all normal node maintenance action\.
  +  If `clustermgtd` replaced or terminated the node, there should be a message detailing why this action was taken on the node\. If the reason is scheduler related \(for example, because the node is in `DOWN`\), check in `slurmctld` log for more information\. If the reason is Amazon EC2 related, there should be informative message detailing the Amazon EC2 related issue that required the replacement\. 
  +  If `clustermgtd` didn’t terminate the node, first check if this was an expected termination by Amazon EC2, more specifically a spot termination\. `computemgtd`, running on a Compute node, can also take an action to terminate a node if `clustermgtd` is determined as unhealthy\. Check `computemgtd` log \(`/var/log/parallelcluster/computemgtd`\) to see if `computemgtd` terminated the node\.
+  **Nodes failed ** 
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

### **Replacing, terminating, or powering down problem instances and nodes**<a name="replacing-terminating-or-powering-down-problematic-instances-and-nodes"></a>
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/parallelcluster/slurm_suspend.log` \(head node\)
+ In most cases, `clustermgtd` handles all expected instance termination action\. Check in the `clustermgtd` log to see why it failed to replace or terminate a node\.
+ For dynamic nodes failing [`scaledown_idletime`](scaling-section.md#scaledown-idletime), check in the `SuspendProgram` log to see if `SuspendProgram` was called by `slurmctld` with the specific node as argument\. Note that `SuspendProgram` doesn’t actually perform any action\. Rather, it only logs when it’s called\. All instance termination and `NodeAddr` reset is done by `clustermgtd`\. Slurm puts nodes back into a `POWER_SAVING` state after `SuspendTimeout` automatically\.

### **Troubleshooting other known node and job issues**<a name="troubleshooting-other-known-node-and-job-issues"></a>

 Another type of known issue is that AWS ParallelCluster might fail to allocate jobs or make scaling decisions\. With this type of issue, AWS ParallelCluster only launches, terminates, or maintains resources according to Slurm instructions\. For these issues, check the `slurmctld` log to troubleshoot these issues\.

## Troubleshooting issues in single queue mode clusters<a name="troubleshooting-issues-in-single-queue-clusters"></a>

**Note**  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\.

 This section applies to clusters that don’t have multiple queue mode with one of the following two configurations:
+ Launched using a AWS ParallelCluster version earlier than 2\.9\.0 and SGE, Torque, or Slurm job schedulers\.
+ Launched using AWS ParallelCluster version 2\.9\.0 or later and SGE or Torque job schedulers\.

### Key logs<a name="key-logs-1"></a>

 The following log files are the key logs for the head node\.

For AWS ParallelCluster version 2\.9\.0 or later:

`/var/log/chef-client.log`  
This is the CINC \(chef\) client log\. It contains all commands that were run through CINC\. It's useful for troubleshooting initialization issues\.

Forall AWS ParallelCluster versions:

`/var/log/cfn-init.log`  
This is the `cfn-init` log\. It contains all commands that were run when an instance is set up, and is therefore useful for troubleshooting initialization issues\. For more information, see [cfn\-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html)\.

`/var/log/jobwatcher`  
This is the `jobwatcher` log\. `jobwatcher` monitors the scheduler queue and updates the Auto Scaling Group\. It's useful for troubleshooting issues related to scaling up nodes\.

`/var/log/sqswatcher`  
This is the `sqswatcher` log\. `sqswatcher` processes the instance ready event sent by a compute instance after successful initialization\. It also adds compute nodes to the scheduler configuration\. This log is useful for troubleshooting why a node or nodes failed to join a cluster\.

The following are the key logs for the compute nodes\.

AWS ParallelCluster version 2\.9\.0 or later

`/var/log/cloud-init-output.log`  
This is the Cloud init log\. It contains all commands that were run when an instance is set up\. It's useful for troubleshooting initialization issues\.

AWS ParallelCluster versions before 2\.9\.0

`/var/log/cfn-init.log`  
This is the CloudFormation init log\. It contains all commands that were run when an instance is set up\. It's useful for troubleshooting initialization issues

All versions

`/var/log/nodewatcher`  
This is the `nodewatcher` log\. `nodewatcher` daemons that run on each Compute node\. They scale down a node if it’s idle\. This log is useful for any issues related to scaling down resources\.

### **Troubleshooting failed launch and join operations**<a name="troubleshooting-failed-launch-and-join-operations"></a>
+ **Applicable logs:**
  + `/var/log/cfn-init-cmd.log` \(head node and compute node\)
  + `/var/log/sqswatcher` \(head node\)
+ If nodes failed to launch, check in the `/var/log/cfn-init-cmd.log` log to see the specific error message\. In most cases, node launch failures are due to a setup failure\.
+  If compute nodes failed to join the scheduler configuration despite successful setup, check the `/var/log/sqswatcher` log to see whether `sqswatcher` processed the event\. Theseissues in most cases are because `sqswatcher` didn’t process the event\.

### Troubleshooting scaling issues<a name="troubleshooting-scaling-issues"></a>
+ **Applicable logs:**
  + `/var/log/jobwatcher` \(head node\)
  + `/var/log/nodewatcher` \(compute node\)
+ **Scale up issues:** For the head node, check the `/var/log/jobwatcher` log to see if the `jobwatcher` daemon calculated the proper number of required nodes and updated the Auto Scaling Group\. Note that `jobwatcher` monitors the scheduler queue and updates the Auto Scaling Group\.
+ **Scale down issues:** For compute nodes, check the `/var/log/nodewatcher` log on the problem node to see why the node was scaled down\. Note that `nodewatcher` daemons scale down a compute node if it’s idle\.

### Troubleshooting other cluster related issues<a name="troubleshooting-other-cluster-related-issues"></a>

One known issue is random compute note fails on large\-scale clusters, specifically those with 500 or more compute nodes\. This issue is related to a limitation of the scaling architecture of single queue cluster\. If you want to use a large scale cluster, are using AWS ParallelCluster version v2\.9\.0 or later, are using Slurm, and want to avoid this issue, you should upgrade and switch to a multiple queue mode supported cluster\. You can do so by running [`pcluster-config convert`](pcluster-config.md#pcluster-config-convert)\.

For ultra\-large scale clusters, additional tuning to your system might be required\. For more information, contact AWS Support\.

## Placement groups and instance launch issues<a name="placement-groups-and-instance-launch-issues"></a>

To get the lowest inter\-node latency, use a *placement group*\. A placement group guarantees that your instances are on the same networking backbone\. If there aren't enough instances available when a request is made, an `InsufficientInstanceCapacity` error is returned\. To reduce the possibility of receiving this error when using cluster placement groups, set the [`placement_group`](cluster-definition.md#placement-group) parameter to `DYNAMIC` and set the [`placement`](cluster-definition.md#placement) parameter to `compute`\.

If you require a high performance shared filesystem, consider using [FSx for Lustre](http://aws.amazon.com/fsx/lustre/)\.

If the head node must be in the placement group, use the same instance type and subnet for both the head as well as all of the compute nodes\. By doing this, the [`compute_instance_type`](cluster-definition.md#compute-instance-type) parameter has the same value as the [`master_instance_type`](cluster-definition.md#master-instance-type) parameter, the [`placement`](cluster-definition.md#placement) parameter is set to `cluster`, and the [`compute_subnet_id`](vpc-section.md#compute-subnet-id) parameter isn't specified\. With this configuration, the value of the [`master_subnet_id`](vpc-section.md#master-subnet-id) parameter is used for the compute nodes\.

For more information, see [Troubleshooting instance launch issues](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html) and [Placement groups roles and limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#concepts-placement-groups) in the *Amazon EC2 User Guide for Linux Instances*

## Directories that cannot be replaced<a name="directories-cannot-be-replaced"></a>

The following directories are shared between the nodes and cannot be replaced\.

`/home`  
This includes the default user home folder \(`/home/ec2_user` on Amazon Linux, `/home/centos` on CentOS, and `/home/ubuntu` on Ubuntu\)\.

`/opt/intel`  
This includes Intel MPI, Intel Parallel Studio, and related files\.

`/opt/sge`  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\.
This includes Son of Grid Engine and related files\. \(Conditional, only if [`scheduler`](cluster-definition.md#scheduler)` = sge`\.\)

`/opt/slurm`  
This includes Slurm Workload Manager and related files\. \(Conditional, only if [`scheduler`](cluster-definition.md#scheduler)` = slurm`\.\)

`/opt/torque`  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\.
This includes Torque Resource Manager and related files\. \(Conditional, only if [`scheduler`](cluster-definition.md#scheduler)` = torque`\.\)

## Troubleshooting issues in NICE DCV<a name="nice-dcv-troubleshooting"></a>

The logs for NICE DCV are written to files in the `/var/log/dcv/` directory\. Reviewing these logs can help to troubleshoot issues\.

The instance type should have at least 1\.7 gibibyte \(GiB\) of RAM to run NICE DCV\. Nano and micro instance types don't have enough memory to run NICE DCV\.

## Troubleshooting issues in clusters with AWS Batch integration<a name="clusters-with-aws-batch-integration"></a>

 This section is relevant to clusters with AWS Batch scheduler integration\.

### Head node issues<a name="head-node-issues"></a>

 Head node related setup issues can be troubleshooted in the same way as single queue cluster\. For more information about these issues, see [Troubleshooting issues in single queue mode clusters](#troubleshooting-issues-in-single-queue-clusters)\.

### AWS Batch multi\-node parallel jobs submission issues<a name="troubleshooting-aws-batch-mnp"></a>

If you have problems submitting multi\-node parallel jobs when using AWS Batch as the job scheduler, you should upgrade to AWS ParallelCluster version 2\.5\.0\. If that's not feasible, you can use the workaround that's detailed in the topic: [Self patch a cluster used for submitting multi\-node parallel jobs through AWS Batch](https://github.com/aws/aws-parallelcluster/wiki/Self-patch-a-Cluster-Used-for-Submitting-Multi-node-Parallel-Jobs-through-AWS-Batch)\.

### Compute issues<a name="compute-issues"></a>

AWS Batch manages the scaling and compute aspects of your services\. If you encounter compute related issues, see the AWS Batch [troubleshooting](https://docs.aws.amazon.com/batch/latest/userguide/troubleshooting.html) documentation for help\.

### Job failures<a name="job-failures"></a>

If a job fails, you can run the ``awsbout`` command to retrieve the job output\. You can also run the ``awsbstat` -d` command to obtain a link to the job logs stored by Amazon CloudWatch\.

## Troubleshooting when a resource fails to create<a name="troubleshooting-resource-fails-to-create"></a>

This section is relevant to cluster resources when they fail to create\.

When a resource fails to create, ParallelCluster returns an error message like the following\.

```
pcluster create -c config my-cluster
Beginning cluster creation for cluster: my-cluster
WARNING: The instance type 'p4d.24xlarge' cannot take public IPs. Please make sure that the subnet with 
id 'subnet-1234567890abcdef0' has the proper routing configuration to allow private IPs reaching the 
Internet (e.g. a NAT Gateway and a valid route table).
WARNING: The instance type 'p4d.24xlarge' cannot take public IPs. Please make sure that the subnet with
id 'subnet-1234567890abcdef0' has the proper routing configuration to allow private IPs reaching the Internet 
(e.g. a NAT Gateway and a valid route table).
Info: There is a newer version 3.0.3 of AWS ParallelCluster available.
Creating stack named: parallelcluster-my-cluster
Status: parallelcluster-my-cluster - ROLLBACK_IN_PROGRESS                   
Cluster creation failed.  Failed events:
- AWS::CloudFormation::Stack MasterServerSubstack Embedded stack 
arn:aws:cloudformation:region-id:123456789012:stack/parallelcluster-my-cluster-MasterServerSubstack-ABCDEFGHIJKL/a1234567-b321-c765-d432-dcba98766789 
was not successfully created: 
The following resource(s) failed to create: [MasterServer]. 
- AWS::CloudFormation::Stack parallelcluster-my-cluster-MasterServerSubstack-ABCDEFGHIJKL The following resource(s) failed to create: [MasterServer]. 
- AWS::EC2::Instance MasterServer You have requested more vCPU capacity than your current vCPU limit of 0 allows for the instance bucket that the 
specified instance type belongs to. Please visit http://aws.amazon.com/contact-us/ec2-request to request an adjustment to this limit.  
(Service: AmazonEC2; Status Code: 400; Error Code: VcpuLimitExceeded; Request ID: a9876543-b321-c765-d432-dcba98766789; Proxy: null)
}
```

As an example, if you see the status message shown in the previous command response, you must use instance types that won't exceed your current vCPU limit or request more vCPU capacity\.

You can also use the CloudFormation console to see information about the `"Cluster creation failed"` status\.

View CloudFormation error messages from the console\.

1. Log in to the AWS Management Console and navigate to [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Select the stack named parallelcluster\-*cluster\_name*\.

1. Choose the **Events** tab\.

1. Check the **Status** for the resource that failed to create by scrolling through the list of resource events by **Logical ID**\. If a subtask failed to create, work backwards to find the failed resource event\.

1. An example of a AWS CloudFormation error message:

   ```
   2022-02-07 11:59:14 UTC-0800	MasterServerSubstack	CREATE_FAILED	Embedded stack 
   arn:aws:cloudformation:region-id:123456789012:stack/parallelcluster-my-cluster-MasterServerSubstack-ABCDEFGHIJKL/a1234567-b321-c765-d432-dcba98766789
   was not successfully created: The following resource(s) failed to create: [MasterServer].
   ```

## Troubleshooting IAM policy size issues<a name="troubleshooting-policy-size-issues"></a>

Refer to [IAM and AWS STS quotas, name requirements, and character limits](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html) to verify quotas on managed policies attached to roles\. If a managed policy size exceeds the quota, split the policy into two or more policies\. If you exceed the quota on the number of policies attached to an IAM role, create additional roles and distribute the policies among them to meet the quota\.

## Additional support<a name="getting-support"></a>

For a list of known issues, see the main [GitHub Wiki](https://github.com/aws/aws-parallelcluster/wiki) page or the [issues](https://github.com/aws/aws-parallelcluster/issues) page\. For more urgent issues, contact AWS Support or open a [new GitHub issue](https://github.com/aws/aws-parallelcluster/issues)\.