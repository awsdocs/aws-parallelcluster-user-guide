# Custom bootstrap actions<a name="custom-bootstrap-actions-v3"></a>

If you define the [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeStart`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeStart) configuration settings, AWS ParallelCluster runs arbitrary code immediately after the node starts\. If you define the [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeConfigured`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeConfigured) configuration settings, AWS ParallelCluster runs the code after the node configuration is correctly completed\.

Starting with AWS ParallelCluster version 3\.4\.0, the code can be run after the head node update, if you define the [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeUpdated`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeUpdated) configuration settings\.

In most cases, this code is stored in Amazon Simple Storage Service \(Amazon S3\) and accessed through an HTTPS connection\. The code is run as `root` and can be in any script language that's supported by the cluster OS\. Often the code is in *Bash* or *Python*\.

**Warning**  
You are responsible for configuring the custom scripts and arguments as described in the [Shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/)\. Verify that your custom scripts and arguments are from sources that you trust to have full access to your cluster nodes\.

**Warning**  
AWS ParallelCluster doesn't support the use of internal variables that are provided through the `/etc/parallelcluster/cfnconfig` file\. This file might be removed as part of a future release\.

`OnNodeStart` actions are called before any node deployment bootstrap action is started, such as configuring NAT, Amazon Elastic Block Store \(Amazon EBS\) or the scheduler\. `OnNodeStart` bootstrap actions may include modifying storage, adding extra users, and adding packages\.

**Note**  
If you configure [`DirectoryService`](DirectoryService-v3.md) and a [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeStart`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeStart) script for your cluster, AWS ParallelCluster configures `DirectoryService` and restarts the `sssd`, before it runs the `OnNodeStart` script\.

`OnNodeConfigured` actions are called after the node bootstrap processes are complete\. `OnNodeConfigured` actions serve the last actions to occur before an instance is considered fully configured and complete\. Some `OnNodeConfigured` actions include changing scheduler settings, modifying storage, and modifying packages\. You can pass argument to scripts by specifying them during configuration\.

`OnNodeUpdated` actions are called after the head node update is completed and the scheduler and shared storage are aligned with the latest cluster configuration changes\.

When `OnNodeStart` or `OnNodeConfigured` custom actions succeed, success is indicated with exit code zero \(0\)\. Any other exit code indicates the instance bootstrap failed\.

When `OnNodeUpdated` custom actions succeed, success is signaled with exit code zero \(0\)\. Any other exit code indicates the update failed\.

**Note**  
If you configure [`OnNodeUpdated`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeUpdated), you must manually restore the `OnNodeUpdated` actions to the previous state on update failures\.  
If an `OnNodeUpdated` custom action fails, the update rolls back to the previous state\. However, the `OnNodeUpdated` action is only run at update time and not at stack rollback time\.

You can specify different scripts for the head node and for each queue, in the [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`CustomActions`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-CustomActions) configuration sections\. [`OnNodeUpdated`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeUpdated) can only be configured in the `HeadNode` section\.

**Note**  
Before AWS ParallelCluster version 3\.0, it was not possible to specify different scripts for head and compute nodes\. Please refer to [Moving from AWS ParallelCluster 2\.x to 3\.x](moving-from-v2-to-v3.md)\.

**Configuration**

The following configuration settings are used to define [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeStart`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeStart) & [`OnNodeConfigured`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeConfigured) & [`OnNodeUpdated`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeUpdated) and [`Scheduling`](Scheduling-v3.md) / [`CustomActions`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-CustomActions) / [`OnNodeStart`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeStart) & [`OnNodeConfigured`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeConfigured) actions and arguments\.

```
HeadNode:
  [...]
  CustomActions:
    OnNodeStart:
      # Script URL. This is run before any of the bootstrap scripts are run
      Script: s3://bucket-name/on-node-start.sh
      Args:
        - arg1
    OnNodeConfigured:
      # Script URL. This is run after all the bootstrap scripts are run
      Script: s3://bucket-name/on-node-configured.sh
      Args:
        - arg1
    OnNodeUpdated:
      # Script URL. This is run after the head node update is completed.
      Script: s3://bucket-name/on-node-updated.sh
      Args:
        - arg1
  # Bucket permissions
  Iam:
    S3Access:
      - BucketName: bucket_name
        EnableWriteAccess: false
Scheduling:
  Scheduler: slurm
   [...]
  SlurmQueues:
    - Name: queue1
      [...]
      CustomActions:
        OnNodeStart:
          Script: s3://bucket-name/on-node-start.sh
          Args:
            - arg1
        OnNodeConfigured:
          Script: s3://bucket-name/on-node-configured.sh
          Args:
            - arg1
      Iam:
        S3Access:
          - BucketName: bucket_name
            EnableWriteAccess: false
```

Using the `Sequence` setting \(added in AWS ParallelCluster version 3\.6\.0\):

```
HeadNode:
  [...]
  CustomActions:
    OnNodeStart:
      # Script URLs. The scripts are run in the same order as listed in the configuration, before any of the bootstrap scripts are run.
      Sequence:
        - Script1: s3://bucket-name/on-node-start1.sh
          Args:
            - arg1
        - Script2: s3://bucket-name/on-node-start2.sh
          Args:
            - arg1
        [...]
    OnNodeConfigured:
      # Script URLs. The scripts are run in the same order as listed in the configuration, after all the bootstrap scripts are run.
      Sequence:
        - Script1: s3://bucket-name/on-node-configured1.sh
          Args:
            - arg1
        - Script2: s3://bucket-name/on-node-configured2.sh
          Args:
            - arg1
        [...]
    OnNodeUpdated:
      # Script URLs. The scripts are run in the same order as listed in the configuration, after the head node update is completed.
      Sequence:
        - Script1: s3://bucket-name/on-node-updated1.sh
          Args:
            - arg1
        - Script2: s3://bucket-name/on-node-updated2.sh
          Args:
            - arg1
        [...]
  # Bucket permissions
  Iam:
    S3Access:
      - BucketName: bucket_name
        EnableWriteAccess: false
Scheduling:
  Scheduler: slurm
   [...]
  SlurmQueues:
    - Name: queue1
      [...]
      CustomActions:
        OnNodeStart:
          # Script URLs. The scripts are run in the same order as listed in the configuration, before any of the bootstrap scripts are run
          Sequence:
            - Script1: s3://bucket-name/on-node-start1.sh
              Args:
                - arg1
            - Script2: s3://bucket-name/on-node-start2.sh
              Args:
                - arg1
            [...]
        OnNodeConfigured:
          # Script URLs. The scripts are run in the same order as listed in the configuration, after all the bootstrap scripts are run
          Sequence:
            - Script1: s3://bucket-name/on-node-configured1.sh
              Args:
                - arg1
            - Script2: s3://bucket-name/on-node-configured2.sh
              Args:
                - arg1
            [...]
      Iam:
        S3Access:
          - BucketName: bucket_name
            EnableWriteAccess: false
```

The `Sequence` setting is added starting with AWS ParallelCluster version 3\.6\.0\. When you specify `Sequence`, you can list multiple scripts for a custom action\. AWS ParallelCluster continues to support configuring a custom action with a single script, without including `Sequence`\.

AWS ParallelCluster doesn't support including both a single script and `Sequence` for the same custom action\. For example, AWS ParallelCluster fails if you specify the following configuration\.

```
[...]
  CustomActions:
    OnNodeStart:
      # Script URL. This is run before any of the bootstrap scripts are run
      Script: s3://bucket-name/on-node-start.sh
          Args:
            - arg1
      # Script URLs. The scripts are run in the same order as listed in the configuration, before any of the bootstrap scripts are run.
      Sequence:
        - Script1: s3://bucket-name/on-node-start1.sh
          Args:
            - arg1
        - Script2: s3://bucket-name/on-node-start2.sh
          Args:
            - arg1
[...]
```

**Arguments**

**Note**  
In AWS ParallelCluster 2\.x the `$1` arguments was a reserved one, to store the URL of the custom script\. If you want to re\-use the custom bootstrap scripts created for AWS ParallelCluster 2\.x with AWS ParallelCluster 3\.x you need to adapt them by considering the shift of the arguments\. Please refer to [Moving from AWS ParallelCluster 2\.x to 3\.x](moving-from-v2-to-v3.md)\.

**Example**

The following steps create a simple script to be executed after the node is configured, that installs the `R,` `curl` and `wget` packages in the nodes of the cluster\.

1. Create a script\.

   ```
   #!/bin/bash
   echo "The script has $# arguments"
   for arg in "$@"
   do
       echo "arg: ${arg}"
   done
   yum -y install "${@:1}"
   ```

1. Upload the script with the correct permissions to Amazon S3\. If public read permissions aren't appropriate for you, use [`HeadNode`](HeadNode-v3.md) / [`Iam`](HeadNode-v3.md#HeadNode-v3-Iam) / [`S3Access`](HeadNode-v3.md#yaml-HeadNode-Iam-S3Access) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) configuration sections\. For more information, see [Working with Amazon S3](s3_resources-v3.md)\.

   ```
   $ aws s3 cp --acl public-read /path/to/myscript.sh s3://<bucket-name>/myscript.sh
   ```
**Important**  
If the script was edited on Windows, line endings must be changed from CRLF to LF before the script is uploaded to Amazon S3\.

1. Update the AWS ParallelCluster configuration to include the new `OnNodeConfigured` action\.

   ```
     CustomActions:
       OnNodeConfigured:
         Script: https://<bucket-name>.s3.<region>.amazonaws.com/myscript.sh
         Args:
           - "R"
           - "curl"
           - "wget"
   ```

   If the bucket doesn't have public\-read permission, use `s3` as the URL protocol\.

   ```
     CustomActions:
       OnNodeConfigured:
         Script: s3://<bucket-name>/myscript.sh
         Args:
           - "R"
           - "curl"
           - "wget"
   ```

1. Launch the cluster\.

   ```
   $ pcluster create-cluster --cluster-name mycluster \
     --region <region> --cluster-configuration config-file.yaml
   ```

1. Verify the output\.
   + If you added custom actions to the `HeadNode` configuration, log in to the head node and check the `cfn-init.log` file located at `/var/log/cfn-init.log` by running following command:

     ```
     $ less /var/log/cfn-init.log
     2021-09-03 10:43:54,588 [DEBUG] Command run
     postinstall output: The script has 3 arguments
     arg: R
     arg: curl
     arg: wget
     Loaded plugins: dkms-build-requires, priorities, update-motd, upgrade-helper
     Package R-3.4.1-1.52.amzn1.x86_64 already installed and latest version
     Package curl-7.61.1-7.91.amzn1.x86_64 already installed and latest version
     Package wget-1.18-4.29.amzn1.x86_64 already installed and latest version
     Nothing to do
     ```
   + If you added custom actions to the `SlurmQueues` setting, check the `cloud-init.log` located at `/var/log/cloud-init.log` in a compute node\. Use CloudWatch to view these logs\.

   You can view both of these logs in the Amazon CloudWatch console\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md)\.