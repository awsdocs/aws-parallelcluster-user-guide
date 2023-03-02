# AMI patching and EC2 instance replacement<a name="instance-updates-ami-patch-v3"></a>

To ensure that all dynamically launched cluster compute nodes behave in a consistent manner, AWS ParallelCluster disables cluster instance automatic OS updates\. Additionally, a specific set of AWS ParallelCluster AMIs are built for each version of AWS ParallelCluster and its associated CLI\. This specific set of AMIs remain unchanged and they are only supported by the AWS ParallelCluster version they were built for\. AWS ParallelCluster AMIs for released versions aren't updated\.

However, due to emergent security issues, customers might want to add patches to these AMIs and then update their clusters with the patched AMI\. This aligns with the [AWS ParallelCluster Shared Responsibility Model](security.md)\.

To view the specific set of AWS ParallelCluster AMIs supported by the AWS ParallelCluster CLI version you are currently using, run:

```
$ pcluster version
$ pcluster list-official-images
```

The AWS ParallelCluster head node is a static instance and you can manually update it\. Restart and reboot of the head node is fully supported starting with AWS ParallelCluster version 3\.0\.0\.

If your instances have ephemeral instance stores, you must remember to save instance store data before manual updates\. For more information, see the [`HeadNode`](HeadNode-v3.md) / [`LocalStorage`](HeadNode-v3.md#HeadNode-v3-LocalStorage) / [`EphemeralVolume`](HeadNode-v3.md#yaml-HeadNode-LocalStorage-EphemeralVolume) cluster configuration and [Instance types with instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes) in the *Amazon EC2 User Guide for Linux Instances*\.

The compute nodes are ephemeral instances\. By default you can only access them from the head node\. Starting with AWS ParallelCluster version 3\.0\.0, you can update the AMI associated with compute instances by modifying the [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Image`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Image) / [`CustomAmi`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Image-CustomAmi) parameter and running the [`pcluster update-cluster`](pcluster.update-cluster-v3.md) command, after stopping the compute fleet with [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md):

```
$ pcluster update-compute-fleet-status --status STOP_REQUESTED
```

It's possible to automate the creation of an updated custom AMI for the compute nodes by using one of the following methods:
+ Use the [`pcluster build-image`](pcluster.build-image-v3.md) command with an updated [Build](Build-v3.md) / [`ParentImage`](Build-v3.md#yaml-build-image-Build-ParentImage)\.
+ Run the build with [`Build`](Build-v3.md) / [`UpdateOsPackages`](Build-v3.md#Build-v3-UpdateOsPackages) / [`Enabled`](Build-v3.md#yaml-build-image-Components-UpdateOsPackages-Enabled):`true`\.

## Head node instance update or replacement<a name="instance-updates-headnode-v3"></a>

In some circumstances, you might be required to restart or reboot the head node\. For example, this is required when you manually update the OS, or when there's an [AWS instance scheduled retirement](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-retirement.html) that imposes a head node instance restart\.

If your instance doesn't have ephemeral drives, you can stop and start it again at any time\. In the case of a scheduled retirement, starting the stopped instance migrates it to use the new hardware\.

Similarly, you can manually stop and start an instance that doesn't have instance stores\. For this case and for other cases of instances without ephemeral volumes, continue to [Stop and start a cluster's head node](#instance-headnode-stop-start-v3)\.

If your instance has ephemeral drives and its been stopped, the data in the instance store is lost\. You can determine if the instance type used for the head node has instance stores from the table found in [Instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes)\.

## Save data from ephemeral drives<a name="instance-updates-save-data-v3"></a>

Starting with AWS ParallelCluster version 3\.0\.0, the head node restart and reboot is fully supported for every instance type\. However, if instances have an ephemeral drive, its data is lost\. Follow the next steps to preserve your data before a head node restart or reboot\.

To check if you have data that needs to be preserved, view the content in the [`EphemeralVolume`](HeadNode-v3.md#yaml-HeadNode-LocalStorage-EphemeralVolume) / [`MountDir`](HeadNode-v3.md#yaml-HeadNode-LocalStorage-EphemeralVolume-MountDir) folder \(`/scratch` by default\)\.

You can transfer the data to the root volume or the shared storage systems attached to the cluster, such as Amazon FSx, Amazon EFS, or Amazon EBS\. Note that the data transfer to remote storage can incur additional costs\.

After saving the data, continue to [Stop and start a cluster's head node](#instance-headnode-stop-start-v3)\.

## Stop and start a cluster's head node<a name="instance-headnode-stop-start-v3"></a>

1. Verify there aren't any running jobs in the cluster\.

   When using a Slurm scheduler:
   + If the `sbatch` `--no-requeue` option isn't specified, running jobs are requeued\.
   + If the `--no-requeue` option is specified, running jobs fail\.

1. Request a cluster compute fleet stop:

   ```
   $ pcluster update-compute-fleet --cluster-name cluster-name --status STOP_REQUESTED
   {
     "status": "STOP_REQUESTED",
     ...
   }
   ```

1. Wait until the compute fleet status is `STOPPED`:

   ```
   $ pcluster update-compute-fleet --cluster-name cluster-name --status STOP_REQUESTED
   {
     "status": "STOPPED",
     ...
   }
   ```

1. For manual updates with an OS reboot or instance restart, you can use the AWS Management Console or AWS CLI\. The following is an example of using the AWS CLI\.

   ```
   # Retrieve head node instance id
   $ pcluster describe-cluster --cluster-name cluster-name --status STOP_REQUESTED
   {
     "headNode": {
     "instanceId": "i-1234567890abcdef0",
     ...
   },
     ...
   }
   # stop and start the instance
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

1. Start the cluster compute fleet:

   ```
   $ pcluster update-compute-fleet --cluster-name cluster-name --status START_REQUESTED
   {
     "status": "START_REQUESTED",
     ...
   }
   ```
