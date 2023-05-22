# Troubleshooting scaling issues<a name="troubleshooting-v3-scaling-issues"></a>

This section is relevant to clusters that were installed using AWS ParallelCluster version 3\.0\.0 and later with the Slurm job scheduler\. For more information about configuring multiple queues, see [Configuration of multiple queues](configuration-of-multiple-queues-v3.md)\.

If one of your running clusters is experiencing issues, place the cluster in a `STOPPED` state by running the following command before you begin to troubleshoot\. This prevents incurring any unexpected costs\.

```
$ pcluster update-compute-fleet --cluster-name mycluster \
   --status STOP_REQUESTED
```

You can list the log streams available from the cluster nodes by using the [`pcluster list-cluster-log-streams`](pcluster.list-cluster-log-streams-v3.md) command and filtering by using the `private-dns-name` of one of the failing nodes or the head node:

```
$ pcluster list-cluster-log-streams --cluster-name mycluster --region eu-west-1 \
 --filters 'Name=private-dns-name,Values=ip-10-0-0-101'
```

Then, you can retrieve the content of the log stream to analyze it by using the [`pcluster get-cluster-log-events`](pcluster.get-cluster-log-events-v3.md) command and passing the `--log-stream-name` corresponding to one of the key logs mentioned in the following section:

```
$ pcluster get-cluster-log-events --cluster-name mycluster \
--region eu-west-1 --log-stream-name ip-10-0-0-13.i-04e91cc1f4ea796fe.cfn-init
```

AWS ParallelCluster creates cluster CloudWatch log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

**Topics**
+ [Key logs for debugging](#troubleshooting-v3-key-logs)
+ [Seeing `InsufficientInstanceCapacity` error in `slurm_resume.log` when I fail to run a job, or in `clustermgtd.log` when I fail to create a cluster](#compute-node-initialization-ice-failure-v3)
+ [Troubleshooting node initialization issues](#troubleshooting-v3-node-init)
+ [**Troubleshooting unexpected node replacements and terminations**](#troubleshooting-v3-unexpected-node-replacements-and-terminations)
+ [**Replacing, terminating, or powering down problematic instances and nodes**](#replacing-terminating-or-powering-down-problematic-instances-and-nodes-v3)
+ [Queue \(partition\) `Inactive` status](#troubleshooting-v3-queues)
+ [Troubleshooting other known node and job issues](#troubleshooting-v3-other-node-job-issues)

## Key logs for debugging<a name="troubleshooting-v3-key-logs"></a>

The following table provides an overview of the key logs for the head node:
+  `/var/log/cfn-init.log` \- This is the AWS CloudFormation init log\. It contains all commands that were run when an instance was set up\. Use it to troubleshoot initialization issues\.
+  `/var/log/chef-client.log` \- This is the Chef client log\. It contains all commands that were run through Chef/CINC\. Use it to troubleshoot initialization issues\.
+  `/var/log/parallelcluster/slurm_resume.log` \- This is a `ResumeProgram` log\. It launches instances for dynamic nodes\. Use it to troubleshoot dynamic nodes launch issues\.
+  `/var/log/parallelcluster/slurm_suspend.log` \- This is the `SuspendProgram` log\. It's called when instances are terminated for dynamic nodes\. Use it to troubleshoot dynamic nodes termination issues\. When you check this log, you should also check the `clustermgtd` log\.
+  `/var/log/parallelcluster/clustermgtd` \- This is the `clustermgtd` log\. It runs as the centralized daemon that manages most cluster operation actions\. Use it to troubleshoot any launch, termination, or cluster operation issues\.
+  `/var/log/slurmctld.log` \- This is the Slurm control daemon log\. AWS ParallelCluster doesn't make scaling decisions\. Rather, it only attempts to launch resources to satisfy the Slurm requirements\. It's useful for scaling and allocation issues, job\-related issues, and any scheduler\-related launch and termination issues\.
+  `/var/log/parallelcluster/compute_console_output` \- This log records the console output from a sample subset of static compute nodes that have unexpectedly terminated\. Use this log if static compute nodes terminate and the compute node logs aren't available in CloudWatch\. The `compute_console_output log` content you receive is the same when you use the EC2 console or AWS CLI to retrieve the instance console output\.

These are the key logs for the compute nodes:
+  `/var/log/cloud-init-output.log` \- This is the [cloud\-init](https://cloudinit.readthedocs.io/) log\. It contains all commands that were run when an instance was set up\. Use it to troubleshoot initialization issues\.
+  `/var/log/parallelcluster/computemgtd` \- This is the `computemgtd` log\. It runs on each compute node to monitor the node in the uncommon event that `clustermgtd` daemon on the head node is offline\. Use it to troubleshoot unexpected termination issues\.
+  `/var/log/slurmd.log` \- This is the Slurm compute daemon log\. Use it to troubleshoot initialization and compute failure issues\.

## Seeing `InsufficientInstanceCapacity` error in `slurm_resume.log` when I fail to run a job, or in `clustermgtd.log` when I fail to create a cluster<a name="compute-node-initialization-ice-failure-v3"></a>

If the cluster uses a Slurm scheduler, you are experiencing an insufficient capacity issue\. If there aren't enough instances available when an instance launch request is made, an `InsufficientInstanceCapacity` error is returned\.

For static instance capacity, you can find the error in the `clustermgtd` log at `/var/log/parallelcluster/clustermgtd`\.

For dynamic instance capacity, you can find the error in the `ResumeProgram` log at `/var/log/parallelcluster/slurm_resume.log`\.

The message looks similar to the following example:

```
An error occurred (InsufficientInstanceCapacity) when calling the RunInstances/CreateFleet operation...
```

Based on your use case, consider using one of the following methods to avoid getting these types of error messages:
+ Disable the placement group if it's enabled\. For more information, see [Placement groups and instance launch issues](troubleshooting-v3-placemment-groups.md)\.
+ Reserve capacity for the instances and launch them with ODCR \(On\-Demand Capacity Reservations\)\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.
+ Configure multiple compute resources with different instance types\. If your workload doesn't require a specific instance type, you can leverage fast insufficient capacity fail over with multiple compute resources\. For more information, see [Slurm cluster fast insufficient capacity fail\-over](slurm-short-capacity-fail-mode-v3.md)\.
+ Configure multiple instance types in the same compute resource, and leverage the multiple instance type allocation\. For more information about configuring multiple instances, see [Multiple instance type allocation with Slurm](slurm-multiple-instance-allocation-v3.md) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`ComputeResources`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-ComputeResources) / [`Instances`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Instances)\.
+ Move the queue to a different Availability Zone by changing the subnet ID in the cluster configuration [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) / [`SubnetIds`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-SubnetIds)\.
+ If your workload isn't tightly coupled, span the queue across different Availability Zones\. For more information about configuring multiple subnets, see [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) / [`SubnetIds`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-SubnetIds)\.

## Troubleshooting node initialization issues<a name="troubleshooting-v3-node-init"></a>

This section covers how you can troubleshoot node initialization issues\. This includes issues where the node fails to launch, power up, or join a cluster\.

**Topics**
+ [Head node](#troubleshooting-v3-node-init.head-node)
+ [Compute nodes](#troubleshooting-v3-node-init.compute-node)

### Head node<a name="troubleshooting-v3-node-init.head-node"></a>

Applicable logs:
+  `/var/log/cfn-init.log` 
+  `/var/log/chef-client.log` 
+  `/var/log/parallelcluster/clustermgtd` 
+  `/var/log/parallelcluster/slurm_resume.log` 
+  `/var/log/slurmctld.log` 

Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` logs or corresponding log streams\. These logs contain all the actions that were run when the head node was set up\. Most errors that occur during setup should have error messages located in the `/var/log/chef-client.log` log\. If `OnNodeStart` or `OnNodeConfigured` scripts are specified in the configuration of the cluster, double check that the script runs successfully through log messages\.

When a cluster is created, the head node must wait for the compute nodes to join the cluster before it can join the cluster\. Because of this, if the compute nodes fail to join the cluster, then the head node also fails\. You can follow one of these sets of procedures, depending on the type of compute notes you use, to troubleshoot this type of issue:

### Compute nodes<a name="troubleshooting-v3-node-init.compute-node"></a>
+ Applicable logs:
  + `/var/log/cloud-init-output.log`
  + `/var/log/slurmd.log`
+ If a compute node is launched, first check `/var/log/cloud-init-output.log`, which should contain the setup logs similar to the `/var/log/chef-client.log` log on the head node\. Most errors that occur during setup should have error messages located at the `/var/log/cloud-init-output.log` log\. If pre\-install or post\-install scripts are specified in cluster configuration, check that they ran successfully\.
+ If you’re using a custom AMI with modification to the Slurm configuration, then there might be a Slurm\-related error that prevents the compute node from joining the cluster\. For scheduler\-related errors, check the `/var/log/slurmd.log` log\.

**Dynamic compute nodes:**
+ Search the `ResumeProgram` log \(`/var/log/parallelcluster/slurm_resume.log`\) for your compute node name to see if `ResumeProgram` was ever called with the node\. \(If `ResumeProgram` wasn’t ever called, you can check the `slurmctld` log \(`/var/log/slurmctld.log`\) to determine if Slurm ever tried to call `ResumeProgram` with the node\)\.
+ Note that incorrect permissions for `ResumeProgram` might cause `ResumeProgram` to fail silently\. If you’re using a custom AMI with modification to `ResumeProgram` setup, check that the `ResumeProgram` is owned by the `slurm` user and has the `744` \(`rwxr--r--`\) permission\.
+ If `ResumeProgram` is called, check to see if an instance is launched for the node\. If no instance was launched, you can see an error message that describes the launch failure\.
+ If the instance is launched, then there might have been a problem during the setup process\. You should see the corresponding private IP address and instance ID from the `ResumeProgram` log\. Moreover, you can look at corresponding setup logs for the specific instance\. For more information about troubleshooting a setup error with a compute node, see the next section\.

 **Static compute nodes:** 
+ Check the `clustermgtd` \(`/var/log/parallelcluster/clustermgtd`\) log to see if instances were launched for the node\. If they weren’t launched, there should be clear error message detailing the launch failure\. 
+ If instance is launched, there's some issue during setup process\. You should see the corresponding private IP address and instance ID from the `ResumeProgram` log\. Moreover, you can look at the corresponding setup logs for the specific instance\. 

 **Compute nodes backed by Spot Instances:** 
+ If it's the first time you use Spot Instances and the job remains in a PD \(pending state\), double check the `/var/log/parallelcluster/slurm_resume.log` file\. You'll probably find an error like the following:

  ```
  2022-05-20 13:06:24,796 - [slurm_plugin.common:add_instances_for_nodes] - ERROR - Encountered exception when launching instances for nodes (x1) ['spot-dy-t2micro-2']: An error occurred (AuthFailure.ServiceLinkedRoleCreationNotPermitted) when calling the RunInstances operation: The provided credentials do not have permission to create the service-linked role for EC2 Spot Instances.
  ```

  When using Spot Instances, an `AWSServiceRoleForEC2Spot` service\-linked role must exist in your account\. To create this role in your account using the AWS CLI, run the following command:

  ```
  $ aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
  ```

  For more information, see [Working with Spot Instances](spot-v3.md) in the AWS ParallelCluster User Guide and [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.

## **Troubleshooting unexpected node replacements and terminations**<a name="troubleshooting-v3-unexpected-node-replacements-and-terminations"></a>

This section continues to explore how you can troubleshoot node related issues, specifically when a node is replaced or terminated unexpectedly\.
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/slurmctld.log` \(head node\)
  + `/var/log/parallelcluster/computemgtd` \(compute node\)

 **Nodes replaced or terminated unexpectedly** 
+  Check in the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) to see if `clustermgtd` replaced or terminated a node\. Note that `clustermgtd` handles all normal node maintenance action\.
+  If `clustermgtd` replaced or terminated the node, there should be a message detailing why this action was taken on the node\. If the reason is scheduler related \(for example, because the node is in `DOWN`\), check in `slurmctld` log for more information\. If the reason is Amazon EC2 related, there should be informative message detailing the Amazon EC2 related issue that required the replacement\. 
+  If `clustermgtd` didn’t terminate the node, first check if this was an expected termination by Amazon EC2, more specifically a spot termination\. `computemgtd`, running on a compute node, can also terminate a node if `clustermgtd` is determined as unhealthy\. Check `computemgtd` log \(`/var/log/parallelcluster/computemgtd`\) to see if `computemgtd` terminated the node\.

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

## **Replacing, terminating, or powering down problematic instances and nodes**<a name="replacing-terminating-or-powering-down-problematic-instances-and-nodes-v3"></a>
+ **Applicable logs:**
  + `/var/log/parallelcluster/clustermgtd` \(head node\)
  + `/var/log/parallelcluster/slurm_suspend.log` \(head node\)
+ In most cases, `clustermgtd` handles all expected instance termination action\. Check in the `clustermgtd` log to see why it failed to replace or terminate a node\.
+ For dynamic nodes failing [`SlurmSettings` Properties](Scheduling-v3.md#Scheduling-v3-SlurmSettings.properties), check in the `SuspendProgram` log to see if `SuspendProgram` was called by `slurmctld` with the specific node as argument\. Note that `SuspendProgram` doesn’t actually perform any action\. Rather, it only logs when it’s called\. All instance termination and `NodeAddr` reset is done by `clustermgtd`\. Slurm puts nodes back into a `POWER_SAVING` state after `SuspendTimeout` automatically\.
+ If compute nodes are failing continuously due to bootstrap failures, verify if they are being launched with [Slurm cluster protected mode](slurm-protected-mode-v3.md) enabled\. If protected mode isn't enabled, modify the protected mode settings to enable protected mode\. Troubleshoot and fix the bootstrap script\.

## Queue \(partition\) `Inactive` status<a name="troubleshooting-v3-queues"></a>

If you run `sinfo` and the output shows queues with `AVAIL` status of `inact`, your cluster might have [Slurm cluster protected mode](slurm-protected-mode-v3.md) enabled and the queue has been set to the `INACTIVE` state for a pre\-defined period of time\.

## Troubleshooting other known node and job issues<a name="troubleshooting-v3-other-node-job-issues"></a>

Another type of known issue is that AWS ParallelCluster might fail to allocate jobs or make scaling decisions\. With this type of issue, AWS ParallelCluster only launches, terminates, or maintains resources according to Slurm instructions\. For these issues, check the `slurmctld` log to troubleshoot them\.