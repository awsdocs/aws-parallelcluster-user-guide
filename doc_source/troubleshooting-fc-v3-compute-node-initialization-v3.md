# Seeing errors in compute node initializations<a name="troubleshooting-fc-v3-compute-node-initialization-v3"></a>

## Seeing `Node bootstrap error` in `clustermgtd.log`<a name="compute-node-initialization-bootstrap-error-v3"></a>

The problem is related to compute nodes failing to bootstrap\. For information on how to debug a cluster protected mode issue, see [How to debug protected mode](slurm-protected-mode-v3.md#slurm-protected-mode-debug-v3)\.

## I configured on demand capacity reservations \(ODCRs\) or zonal Reserved Instances<a name="compute-node-initialization-odcr-v3"></a>

### ODCRs that include instances that have multiple network interfaces, such as P4d, P4de, and AWS Trainium \(Trn\)<a name="compute-node-initialization-odcr-multi-ni-v3"></a>

In the cluster configuration file, check that the `HeadNode` is in a public subnet and that the compute nodes are in a private subnet\.

### ODCRs are targeted ODCRS<a name="compute-node-initialization-odcr-targeted-v3"></a>

#### Seeing `Unable to read file '/opt/slurm/etc/pcluster/run_instances_overrides.json'.` even though I already have `/opt/slurm/etc/pcluster/run_instances_overrides.json` in place by following the instructions given in [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)<a name="compute-node-initialization-odcr-targeted-noread-v3"></a>

If you are using AWS ParallelCluster versions 3\.1\.1 to 3\.2\.1 with targeted ODCRs, and you are also using the [run instances override JSON file](launch-instances-odcr-v3.md), it's possible that you don’t have the JSON file formatted correctly\. You could see an error in `clustermgtd.log`, such as the following:

```
Unable to read file '/opt/slurm/etc/pcluster/run_instances_overrides.json'. 
Using default: {} in  /var/log/parallelcluster/clustermgtd.
```

Validate that the JSON file format is correct by running the following:

```
$ echo /opt/slurm/etc/pcluster/run_instances_overrides.json | jq
```

#### Seeing `Found RunInstances parameters override.` in `clustermgtd.log` when cluster creation failed, or in `slurm_resume.log` when run job failed<a name="compute-node-initialization-odcr-targeted-override-v3"></a>

If you are using [run instances override JSON file](launch-instances-odcr-v3.md), check that you correctly set the queue name and the compute resources name in the `/opt/slurm/etc/pcluster/run_instances_overrides.json` file\.

#### Seeing `An error occurred (InsufficientInstanceCapacity)` in `slurm_resume.log` when I fail to a run job, or in `clustermgtd.log` when I fail to create a cluster<a name="compute-node-initialization-odcr-ii-capacity-v3"></a>

##### Using PG\-ODCR \(Placement Group ODCR\)<a name="compute-node-initialization-odcr-ii-pg-capacity-v3"></a>

When creating an ODCR with an associated placement group, the same placement group name must be used in the configuration file\. Set the corresponding [placement group name](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) in the cluster configuration\.

##### Using zonal Reserved Instances<a name="compute-node-initialization-odcr-ii-zonal-capacity-v3"></a>

If you are using zonal Reserved Instances with `PlacementGroup` / `Enabled` to `true` in the cluster configuration, you might see an error, such as the following:

```
We currently do not have sufficient trn1.32xlarge capacity in the Availability Zone you requested (us-east-1d). Our system will be working on provisioning additional capacity. 
You can currently get trn1.32xlarge capacity by not specifying an Availability Zone in your request or choosing us-east-1a, us-east-1b, us-east-1c, us-east-1e, us-east-1f.
```

You might see this because the zonal Reserved Instances aren't placed in the same UC \(or spine\), which can cause insufficient capacity errors \(ICEs\) when using placement groups\. You can check this case by disabling the `PlacementGroup`Group setting in the cluster configuration to determine if the cluster can allocate the instances\.

## Seeing `An error occurred (VcpuLimitExceeded)` in `slurm_resume.log` when I fail to run a job, or in `clustermgtd.log`, when I fail to create a cluster<a name="compute-node-initialization-vpc-limit-v3"></a>

Check the vCPU limits on your account for the specific EC2 instance type that you are using\. If you see zero or fewer vCPUs than you are requesting, request an increase for your limits\. For information about how to view current limits and request new limits, see [Amazon EC2 service quotas](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Seeing `An error occurred (InsufficientInstanceCapacity)` in `slurm_resume.log` when I fail to run a job, or in `clustermgtd.log`, when I fail to create a cluster<a name="compute-node-initialization-ice-failure-v3"></a>

You are experiencing an insufficient capacity issue\. Follow [https://aws\.amazon\.com/premiumsupport/knowledge\-center/ec2\-insufficient\-capacity\-errors/](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-insufficient-capacity-errors/) to troubleshoot the issue\.

## Seeing nodes are in `DOWN` state with `Reason (Code:InsufficientInstanceCapacity)...`<a name="compute-node-initialization-down-nodes-v3"></a>

You are experiencing an insufficient capacity issue\. Follow [https://aws\.amazon\.com/premiumsupport/knowledge\-center/ec2\-insufficient\-capacity\-errors/](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-insufficient-capacity-errors/) to troubleshoot the issue\. For more information about AWS ParallelCluster's fast insufficient capacity failover mode, see [Slurm cluster fast insufficient capacity fail\-over](slurm-short-capacity-fail-mode-v3.md)\.

## Seeing `cannot change locale (en_US.utf-8) because it has an invalid name` in `slurm_resume.log`<a name="compute-node-initialization-locale-v3"></a>

This can occur if you have an unsuccessful `yum` installation process that left the locale settings in an inconsistent state\. For example, this can be caused when a user terminates the install process\.

**To verify the cause, take the following actions:**
+ Run `su - pcluster-admin`\.

  The shell shows an error, such as, `cannot change locale...no such file or directory`\.
+ Run `localedef --list`\.

  Returns an empty list or doesn't contain the default locale\.
+ Check the last `yum` command with `yum history` and `yum history info #ID`\. Does the last ID have `Return-Code: Success`?

  If the last ID doesn't have `Return-Code: Success`, the post\-install scripts might not have run successfully\.

To fix the issue, try rebuilding the locale with `yum reinstall glibc-all-langpacks`\. After the rebuild, `su - pcluster-admin` doesn't show an error or warning if the issue is fixed\.

## None of the previous scenarios apply to my situation<a name="compute-node-initialization-not-found-v3"></a>

To troubleshoot compute node initialization issues, see [Troubleshooting node initialization issues](troubleshooting-v3-scaling-issues.md#troubleshooting-v3-node-init)\.

Check to see if your scenario is covered in [GitHub Known Issues](https://github.com/aws/aws-parallelcluster/wiki) at AWS ParallelCluster on GitHub\.

For additional support, see [Additional support](troubleshooting-v3-additional-support.md)\.