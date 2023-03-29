# AMI patching and EC2 instance replacement<a name="instance-updates-ami-patch"></a>

To ensure that all dynamically launched cluster compute nodes behave in a consistent manner, AWS ParallelCluster disables cluster instance automatic OS updates\. Additionally, a specific set of AWS ParallelCluster AMIs are built for each version of AWS ParallelCluster and its associated CLI\. This specific set of AMIs remain unchanged and they are only supported by the AWS ParallelCluster version they were built for\. AWS ParallelCluster AMIs for released versions aren't updated\.

However, due to emergent security issues, customers might want to add patches to these AMIs and then update their clusters with the patched AMI\. This aligns with the [AWS ParallelCluster Shared Responsibility Model](security.md)\.

To view the specific set of AWS ParallelCluster AMIs supported by the AWS ParallelCluster CLI version you are currently using, run:

```
$ pcluster version
```

Then view [amis\.txt](https://github.com/aws/aws-parallelcluster/blob/release-2.11/amis.txt) in AWS ParallelCluster's GitHub repository\.

The AWS ParallelCluster head node is a static instance and you can manually update it\. Restart and reboot of the head node is fully supported starting with AWS ParallelCluster version 2\.11, if the instance type doesn't have an instance store\. For more information, see [Instance types with instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes) in the *Amazon EC2 User Guide for Linux Instances*\. You can't update an AMI for an existing cluster\.

Head node restart and reboot with AMI updates of cluster compute instances is fully supported starting with AWS ParallelCluster version 3\.0\.0\. Consider upgrading to the most recent version 3\.5\.1 to use these features\.

## Head node instance update or replacement<a name="instance-updates-headnode"></a>

In some circumstances, you might be required to restart or reboot the head node\. For example, this is required when you manually update the OS, or when there's an [AWS instance scheduled retirement](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-retirement.html) that imposes a head node instance restart\.

If your instance doesn't have ephemeral drives, you can stop and start it again at any time\. In case of a scheduled retirement, starting the stopped instance migrates it to use the new hardware\.

Similarly, you can manually stop and start an instance that doesn't have instance stores\. For this case and for other cases of instances without ephemeral volumes, continue to [Stop and start a cluster's head node](#instance-headnode-stop-start)\.

If your instance has ephemeral drives and its been stopped, the data in the instance store is lost\. You can determine if the instance type used for the head node has instance stores from the table found in [Instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes)\.

The following sections describe the limitations in using instances with instance store volumes\.

## Instance store limitations<a name="instance-store-limitations"></a>

The limitations in using AWS ParallelCluster version 2\.11 and instance types with an instance store are as follows:
+ When ephemeral drives are not encrypted \([`encrypted_ephemeral`](cluster-definition.md#encrypted-ephemeral) parameter is set to `false` or not set\), an AWS ParallelCluster instance isn't able to boot after an instance stop\. This is because information on old non\-existent ephemerals is written into `fstab` and the OS tries to mount non\-existent storage\.
+ When ephemeral drives are encrypted \([`encrypted_ephemeral`](cluster-definition.md#encrypted-ephemeral) parameter is set to `true`\), an AWS ParallelCluster instance can be started after a stop but the new ephemeral drives aren't setup, mounted, or available\.
+ When ephemeral drives are encrypted, an AWS ParallelCluster instance can be rebooted but old ephemeral drives \(which survive the instance reboot\) can't be accessed because the encryption key is created in the memory that's lost with the reboot\.

The only supported case is the instance reboot, when ephemeral drives aren't encrypted\. This is because the drive survives the reboot and is mounted back because of the entry written in `fstab`\.

## Instance store limitations workarounds<a name="instance-store-workarounds"></a>

First, save your data\. To check if you have data that needs to be preserved, view the content in the [`ephemeral_dir`](cluster-definition.md#ephemeral-dir) folder \(`/scratch` by default\)\. You can transfer the data to the root volume or the shared storage systems attached to the cluster, such as Amazon FSx, Amazon EFS, or Amazon EBS\. Note that the data transfer to remote storage can incur additional costs\.

The root cause of the limitations is in the logic that AWS ParallelCluster uses to format and mount instance store volumes\. The logic adds an entry to `/etc/fstab` of the form:

```
$ /dev/vg.01/lv_ephemeral ${ephemeral_dir} ext4 noatime,nodiratime 0 0
```

`${ephemeral_dir}` is the value of the [`ephemeral_dir`](cluster-definition.md#ephemeral-dir) parameter from the pcluster configuration file \(defaults to `/scratch`\)\.

This line is added so that if or when a node is rebooted, the instance store volumes are re\-mounted automatically\. This is desirable because data in ephemeral drives persists through reboot\. However, data on the ephemeral drives doesn't persist through a start or stop cycle\. This means they are formatted and mounted with no data\.

The only supported case is the instance reboot when ephemeral drives aren't encrypted\. This is because the drive survives the reboot and is mounted back because it's written in `fstab`\.

To preserve the data in all other cases, you must remove the logical volume entry before stopping the instance\. For example, remove `/dev/vg.01/lv_ephemeral` from `/etc/fstab` before stopping the instance\. After doing this, you start the instance without mounting the ephemeral volumes\. However, the instance store mount again won't be available after the stop or start of the instance\.

After saving your data and then removing the `fstab` entry, continue to the next section\.

## Stop and start a cluster's head node<a name="instance-headnode-stop-start"></a>

**Note**  
Starting with AWS ParallelCluster version 2\.11, head node stop and start is only supported if the instance type doesn't have an instance store\.

1. Verify there aren't any running jobs in the cluster\.

   When using a Slurm scheduler:
   + If the `sbatch` `--no-requeue` option isn't specified, running jobs are requeued\.
   + If the `--no-requeue` option is specified, running jobs fail\.

1. Request a cluster compute fleet stop:

   ```
   $ pcluster stop cluster-name
   Compute fleet status is: RUNNING. Submitting status change request.
   Request submitted successfully. It might take a while for the transition to complete.
   Please run 'pcluster status' if you need to check compute fleet status
   ```

1. Wait until the compute fleet status is `STOPPED`:

   ```
   $ pcluster status cluster-name
   ...
   ComputeFleetStatus: STOP_REQUESTED
   $ pcluster status cluster-name
   ...
   ComputeFleetStatus: STOPPED
   ```

1. For manual updates with an OS reboot or instance restart, you can use the AWS Management Console or AWS CLI\. The following is an example of using the AWS CLI\.

   ```
   $ aws ec2 stop-instances --instance-ids 1234567890abcdef0
   {
     "StoppingInstances": [
       {
         "CurrentState": {
           "Name": "stopping"
           ...
         },
         "InstanceId": "i-1234567890abcdef0",
         "PreviousState": {
           "Name": "running"
           ...
         }
       }
     ]
   }
   $ aws ec2 start-instances --instance-ids 1234567890abcdef0
   {
     "StartingInstances": [
       {
         "CurrentState": {
           "Name": "pending"
           ...
         },
         "InstanceId": "i-1234567890abcdef0",
         "PreviousState": {
           "Name": "stopped"
           ...
         }
       }
     ]
   }
   ```

1. Start the compute fleet of the cluster:

   ```
   $ pcluster start cluster-name
   Compute fleet status is: STOPPED. Submitting status change request.
   Request submitted successfully. It might take a while for the transition to complete.
   Please run 'pcluster status' if you need to check compute fleet status
   ```