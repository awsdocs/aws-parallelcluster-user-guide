# Using `pcluster update-cluster`<a name="using-pcluster-update-cluster-v3"></a>

In AWS ParallelCluster 3\.x, [`pcluster update-cluster`](pcluster.update-cluster-v3.md) analyzes the settings used to create the current cluster and the settings in the configuration file for issues\. If any issues are discovered, they are reported, and the steps to take to fix the issues are displayed\. For example, if the compute [`InstanceType`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-InstanceType) is changed, the compute fleet must be stopped before an update can proceed\. This issue is reported when it is discovered\. If no blocking issues are discovered, update process is started and the changes are reported\.

You can use the [`pcluster update-cluster`](pcluster.update-cluster-v3.md) `--dryrun option` to see the changes before their run\. For more information, see [`pcluster update-cluster` examples](#pcluster-update-cluster-examples)\.

For troubleshooting guidance, see [AWS ParallelCluster Troubleshooting](troubleshooting-v3.md)\.

## Update Policy: definitions<a name="pcluster-update-policy-definitions"></a>

**Update policy: This setting can be changed during an update\.**  
After changing this setting, the cluster can be updated using [`pcluster update-cluster`](pcluster.update-cluster-v3.md)\.

**Update policy: If this setting is changed, the update is not allowed\.**  
After changing this setting, the cluster can't be updated\. You must revert the settings for the original cluster and create a new cluster with the updated settings\. You can delete the original cluster at a later date\. To create the new cluster, use [`pcluster create-cluster`](pcluster.create-cluster-v3.md)\. To delete the original cluster, use [`pcluster delete-cluster`](pcluster.delete-cluster-v3.md)\.

**Update policy: This setting is not analyzed during an update\.**  
These settings can be changed, and the cluster updated using [`pcluster update-cluster`](pcluster.update-cluster-v3.md)\.

**Update policy: The compute fleet must be stopped for this setting to be changed for an update\.**  
These settings cannot be changed while the compute fleet exists\. Either the change must be reverted or the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\)\. After the compute fleet is stopped you can update the cluster \([`pcluster update-cluster`](pcluster.update-cluster-v3.md)\) to activate the changes\. For example, if you are using a Slurm scheduler with [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`ComputeResources`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-ComputeResources) / \- [`Name`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Name) / [`MinCount`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-MinCount) > 0, a compute fleet is started\.

**Update policy: This setting can't be decreased during an update\.**  
These settings can be changed, but they cannot be decreased\. If these settings must be decreased, you must revert the settings for the original cluster and create a new cluster with the updated settings\. You can delete the original cluster at a later date\. To create the new cluster, use [`pcluster create-cluster`](pcluster.create-cluster-v3.md)\. To delete the original cluster, use [`pcluster delete-cluster`](pcluster.delete-cluster-v3.md)\.

**Update policy: Reducing the size of a queue below the current number of nodes requires that the compute fleet be stopped first\.**  
These settings can be changed, but if the change would reduce the size of the queue below the current size, the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\)\. After the compute fleet is stopped you can update the cluster \([`pcluster update-cluster`](pcluster.update-cluster-v3.md)\) to activate the changes\.

**Update policy: Reducing the number of static nodes in a queue requires that the compute fleet be stopped first\.**  
These settings can be changed, but if the change would reduce the number of static nodes in the queue below the current size, the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\)\. After the compute fleet is stopped you can update the cluster \([`pcluster update-cluster`](pcluster.update-cluster-v3.md)\) to activate the changes\.

**Update policy: If this setting is changed, the update is not allowed\. If you force the update, the new value will be ignored and the old value will be used\.**  
After changing this setting, the cluster can't be updated\. You must revert the settings for the original cluster and create a new cluster with the updated settings\. You can delete the original cluster at a later date\. To create the new cluster, use [`pcluster create-cluster`](pcluster.create-cluster-v3.md)\. To delete the original cluster, use [`pcluster delete-cluster`](pcluster.delete-cluster-v3.md)\.

**Update policy: The compute fleet must be stopped or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) must be set for this setting to be changed for an update\.**  
These settings can be changed\. Either the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\) or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) must be set\. After the compute fleet is stopped or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) is set, you can update the cluster \([`pcluster update-cluster`](pcluster.update-cluster-v3.md)\) to activate the changes\.  
This update policy is supported starting with AWS ParallelCluster version 3\.2\.0\.

**Update policy: For this list values setting, a new value can be added during an update or the compute fleet must be stopped when removing an existing value\.**  
A new value for these settings can be added during an update\. After adding a new value to the list, the cluster can be updated using \([`pcluster update-cluster`](pcluster.update-cluster-v3.md)\)\.  
To remove an existing value from the list, the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\)\.  
For example, if you are using a Slurm scheduler and adding a new queue to [SlurmQueues](Scheduling-v3.md#Scheduling-v3-SlurmQueues), you can update the cluster without stopping the compute fleet\. To remove an existing queue from [SlurmQueues](Scheduling-v3.md#Scheduling-v3-SlurmQueues), the compute fleet must be stopped first \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\)\.  
This update policy is supported starting with AWS ParallelCluster version 3\.2\.0\.

**Update policy: For this list values setting, the compute fleet must be stopped or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) must be set to add a new value; the compute fleet must be stopped when removing an existing value\.**  
A new value for these settings can be added during an update\. Either the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\) or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) must be set\. After the compute fleet is stopped or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) is set, you can update the cluster \([`pcluster update-cluster`](pcluster.update-cluster-v3.md)\) to activate the changes\.  
To remove an existing value from the list, the compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\)\.  
This update policy is supported starting with AWS ParallelCluster version 3\.3\.0\.

**Update policy: All compute nodes must be stopped for a managed placement group deletion\. The compute fleet must be stopped or [`QueueUpdateStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-QueueUpdateStrategy) must be set for this setting to be changed for an update\.**  
The compute fleet must be stopped \(using [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)\) in order to remove a managed placement group\. If you run a cluster update to remove a managed placement group before stopping the compute fleet, an invalid configuration message is returned and the update doesn't proceed\. Stopping the compute fleet guarantees no instances are running\.

## `pcluster update-cluster` examples<a name="pcluster-update-cluster-examples"></a>
+ This example demonstrates an update with some allowed changes and the update is started directly\.

  ```
  $ pcluster update-cluster --cluster-name cluster_name --cluster-config ~/.parallelcluster/test_cluster --region us-east-1
  {
    "cluster": {
      "clusterName": cluster_name,
      "cloudformationStackStatus": "UPDATE_IN_PROGRESS",
      "cloudformationStackArn": stack_arn,
      "region": "us-east-1",
      "version": "3.4.1",
      "clusterStatus": "UPDATE_IN_PROGRESS"
    },
    "changeSet": [
      {
        "parameter": "HeadNode.Networking.AdditionalSecurityGroups",
        "requestedValue": [
          "sg-0cd61884c4ad11234"
        ],
        "currentValue": [
          "sg-0cd61884c4ad16341"
        ]
      }
    ]
  }
  ```
+ This example demonstrates a dryrun update with some allowed changes\. Dryrun is useful to report the change set without starting the update\.

  ```
  $ pcluster update-cluster --cluster-name cluster_name --cluster-config ~/.parallelcluster/test_cluster --region us-east-1 --dryrun true
  {
    "message": "Request would have succeeded, but DryRun flag is set.",
    "changeSet": [
      {
        "parameter": "HeadNode.Networking.AdditionalSecurityGroups",
        "requestedValue": [
          "sg-0cd61884c4ad11234"
        ],
        "currentValue": [
          "sg-0cd61884c4ad16341"
        ]
      }
    ]
  }
  ```
+ This example demonstrates an update with some changes that block the update\.

  ```
  $ pcluster update-cluster --cluster-name cluster_name --cluster-config ~/.parallelcluster/test_cluster --region us-east-1
  {
    "message": "Update failure",
    "updateValidationErrors": [
      {
        "parameter": "HeadNode.Ssh.KeyName",
        "requestedValue": "mykey_2",
        "message": "Update actions are not currently supported for the 'KeyName' parameter. Restore 'KeyName' value to 'jenkinsjun'. If you need this change, please consider creating a new cluster instead of updating the existing one.",
        "currentValue": "mykey_1"
      },
      {
        "parameter": "Scheduling.SlurmQueues[queue1].ComputeResources[queue1-t2micro].InstanceType",
        "requestedValue": "c4.xlarge",
        "message": "All compute nodes must be stopped. Stop the compute fleet with the pcluster update-compute-fleet command",
        "currentValue": "t2.micro"
      },
      {
        "parameter": "SharedStorage[ebs1].MountDir",
        "requestedValue": "/my/very/very/long/shared_dir",
        "message": "Update actions are not currently supported for the 'MountDir' parameter. Restore 'MountDir' value to '/shared'. If you need this change, please consider creating a new cluster instead of updating the existing one.",
        "currentValue": "/shared"
      }
    ],
    "changeSet": [
      {
        "parameter": "HeadNode.Networking.AdditionalSecurityGroups",
        "requestedValue": [
          "sg-0cd61884c4ad11234"
        ],
        "currentValue": [
          "sg-0cd61884c4ad16341"
        ]
      },
      {
        "parameter": "HeadNode.Ssh.KeyName",
        "requestedValue": "mykey_2",
        "currentValue": "mykey_1"
      },
      {
        "parameter": "Scheduling.SlurmQueues[queue1].ComputeResources[queue1-t2micro].InstanceType",
        "requestedValue": "c4.xlarge",
        "currentValue": "t2.micro"
      },
      {
        "parameter": "SharedStorage[ebs1].MountDir",
        "requestedValue": "/my/very/very/long/shared_dir",
        "currentValue": "/shared"
      }
    ]
  }
  ```