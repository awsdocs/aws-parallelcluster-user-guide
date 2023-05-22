# Troubleshooting cluster health metrics<a name="troubleshooting-v3-cluster-health-metrics"></a>

Cluster health metrics are added to the AWS ParallelCluster Amazon CloudWatch dashboard starting with AWS ParallelCluster version 3\.6\.0\. In the following sections, you can learn about the dashboard health metrics, and actions you can take to troubleshoot and resolve issues\.

**Topics**
+ [Seeing the **Instance Provisioning Errors** graph](#troubleshooting-v3-cluster-health-metrics-instance-provisioning)
+ [Seeing the **Unhealthy Instance Errors** graph](#troubleshooting-v3-cluster-health-metrics-unhealthy-instance)
+ [Seeing the **Compute Fleet Idle Time** graph](#troubleshooting-v3-cluster-health-metrics-idle-time-errors)

## Seeing the **Instance Provisioning Errors** graph<a name="troubleshooting-v3-cluster-health-metrics-instance-provisioning"></a>

If you see a non\-zero value in the `Instance Provisioning Errors` graph, then it means that the EC2 instance for backing slurm nodes failed to launch on the `CreateFleet` or `RunInstance` API\.

### Seeing `IAMPolicyErrors`<a name="troubleshooting-v3-cluster-health-metrics-iam-policy"></a>
+ **What happened?**

  A number of instances failed to launch, which is caused by insufficient permissions with error code `UnauthorizedOperation`\.
+ **How to resolve?**

  If you have a configured a custom [`InstanceRole`](HeadNode-v3.md#yaml-HeadNode-Iam-InstanceRole) or [`InstanceProfile`](HeadNode-v3.md#yaml-HeadNode-Iam-InstanceProfile), check your IAM policies and verify that you are using the correct credentials\.

  Check the `clustermgtd` file for static node error details\. Check the `slurm_resume.log` file for dynamic node error details\. Use the details to learn more about the missing permissions that must be added\.

### Seeing `VcpuLimitErrors`<a name="troubleshooting-v3-cluster-health-metrics-vcpu-limit"></a>
+ **What happened?**

  AWS ParallelCluster failed to launch instances because it reached the vCPU limit on your AWS account for a specific EC2 instance type that you configured for cluster compute nodes\.
+ **How to resolve?**

  Check for the `VcpuLimitExceeded` error in the `clustermgtd` file for static nodes, and check in the `slurm_resume.log` file for dynamic nodes to get additional details\. To resolve this issue, you can request an increase to your vCPU limits\. For more information about how to view current limits and request new limits, see [Amazon EC2 service quotas](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### Seeing `VolumeLimitErrors`<a name="troubleshooting-v3-cluster-health-metrics-volume-limit"></a>
+ **What happened?**

  You have reached your Amazon EBS volume limit on your AWS account, and AWS ParallelCluster is unable to launch instances with error code `InsufficientVolumeCapacity` or `VolumeLimitExceeded`\.
+ **How to resolve?**

  Check the `clustermgtd` file for static nodes, and check the `slurm_resume.log` file for dynamic nodes to get additional volume limit details\. To resolve this issue, you can use a different AWS Region, clean up existing volumes, or contact the AWS Support Center to submit a request to increase your Amazon EBS volume limit\.

### Seeing `InsufficientCapacityErrors`<a name="troubleshooting-v3-cluster-health-metrics-ice"></a>
+ **What happened?**

  AWS ParallelCluster doesn't have sufficient capacity to launch EC2 instances to back nodes\.
+ **How to resolve?**

  Check the `clustermgtd` file for static nodes, and check the `slurm_resume.log` file for dynamic nodes to get insufficient capacity error details\. To troubleshoot the issue, follow the guidance at [https://aws\.amazon\.com/premiumsupport/knowledge\-center/ec2\-insufficient\-capacity\-errors/](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-insufficient-capacity-errors/)\.

### `OtherInstanceLaunchFailures`<a name="troubleshooting-v3-cluster-health-metrics-other-launch-failures"></a>
+ **What happened?**

  The EC2 instance for backing compute nodes failed to launch with the `CreateFleet` or `RunInstance` API\.
+ **How to resolve?**

  Check the `clustermgtd` file for static nodes, and check the `slurm_resume.log` file for dynamic nodes to get error details\.

## Seeing the **Unhealthy Instance Errors** graph<a name="troubleshooting-v3-cluster-health-metrics-unhealthy-instance"></a>
+ **What happened?**

  A number of compute instances were launched but later terminated as unhealthy\.
+ **How to resolve?**

  For more information about troubleshooting unhealthy nodes, see [**Troubleshooting unexpected node replacements and terminations**](troubleshooting-v3-scaling-issues.md#troubleshooting-v3-unexpected-node-replacements-and-terminations)\.

### Seeing `InstanceBootstrapTimeoutError`<a name="troubleshooting-v3-cluster-health-metrics-bootstrap-timeout"></a>
+ **What happened?**

  An instance can't join the cluster within the `resume_timeout` \(for dynamic nodes\) or `node_replacement_timeout` \(for static nodes\)\. This can occur if the network isn't configured correctly for the compute nodes, or it can occur if custom scripts running on the compute node take too long to finish\.
+ **How to resolve?**

  For dynamic nodes, check the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) for the compute node IP address and errors such as the following:

  ```
  Node bootstrap error: Resume timeout expires for node
  ```

  For static nodes, check the `clustermgtd` log \(`/var/log/parallelcluster/clustermgtd`\) for the compute node IP address and errors such as the following:

  ```
  Node bootstrap error: Replacement timeout expires for node ... in replacement.
  ```

  For additional details, check the `/var/log/cloud-init-output.log` file for errors\. You can retrieve problematic compute node IP addresses from the `clustermgtd` and `slurm_resume` log files\.

### Seeing `EC2HealthCheckErrors`<a name="troubleshooting-v3-cluster-health-metrics-ec2-check"></a>
+ **What happened?**

  An instance failed an EC2 health check\.
+ **How to resolve?**

  For information about how to troubleshoot this issue, see [Troubleshoot instances with failed status checks](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)\.

### Seeing `ScheduledEventHealthCheckErrors`<a name="troubleshooting-v3-cluster-health-metrics-ec2-scheduled-event"></a>
+ **What happened?**

  An instance failed an EC2 scheduled event health check, and it's unhealthy\.
+ **How to resolve?**

  For information about how to troubleshoot this issue, see [Scheduled events for your instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-instances-status-check_sched.html)\.

### Seeing `NoCorrespondingInstanceErrors`<a name="troubleshooting-v3-cluster-health-metrics-missing-instances"></a>
+ **What happened?**

  AWS ParallelCluster can't find instances backing nodes\. The nodes have likely self\-terminated during bootstrap operations\. [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`CustomActions`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-CustomActions) / [`OnNodeStart`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeStart) \| [`OnNodeConfigured`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeConfigured) script, or network errors can produce `NoCorrespondingInstanceErrors`\.
+ **How to resolve?**

  For additional details, check the `/var/log/cloud-init-output.log` for the compute node\.

## Seeing the **Compute Fleet Idle Time** graph<a name="troubleshooting-v3-cluster-health-metrics-idle-time-errors"></a>

### Seeing a `MaxDynamicNodeIdleTime` that is significantly longer than the **Idle Time Scaledown** threshold<a name="troubleshooting-v3-cluster-health-idle-time-threshold"></a>
+ **What happened?**

  Your instance isn't terminating properly\. `MaxDynamicNodeIdleTime` shows the maximum time in seconds that a dynamic node, backed by an EC2 instance, is idle\. The **Idle Time Scaledown** threshold is derived from the cluster configuration [`ScaledownIdletime`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-ScaledownIdletime) parameter\. When a compute node has been idle for more than **Idle Time Scaledown** seconds, Slurm powers down the node and AWS ParallelCluster terminates the backing instance\. In this case, something is preventing the instance termination\.
+ **How to resolve?**

  For more information about this issue, see [**Replacing, terminating, or powering down problematic instances and nodes**](troubleshooting-v3-scaling-issues.md#replacing-terminating-or-powering-down-problematic-instances-and-nodes-v3) in [Troubleshooting scaling issues](troubleshooting-v3-scaling-issues.md)\.