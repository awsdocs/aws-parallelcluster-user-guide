# Custom Bootstrap Actions<a name="custom-bootstrap-actions-v3"></a>

AWS ParallelCluster can run arbitrary code either right after the node start, for example [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeStart`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeStart), or when the node configuration is correctly completed, for example [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeConfigured`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeConfigured)\. In most cases, this code is stored in Amazon Simple Storage Service \(Amazon S3\) and accessed through an HTTPS connection\. The code is run as `root` and can be in any script language that's supported by the cluster OS\. Often the code is in *Bash* or *Python*\.

`OnNodeStart` actions are called before any node deployment bootstrap action is started, such as configuring NAT, Amazon Elastic Block Store \(Amazon EBS\) or the scheduler\. `OnNodeStart` bootstrap actions may include modifying storage, adding extra users, and adding packages\.

`OnNodeConfigured` actions are called after the node bootstrap processes are complete\. `OnNodeConfigured` actions serve the last actions to occur before an instance is considered fully configured and complete\. Some `OnNodeConfigured` actions include changing scheduler settings, modifying storage, and modifying packages\. You can pass argument to scripts by specifying them during configuration\.

If a custom action fails, the instance bootstrap also fails\. Success is signaled with an exit code of zero \(0\)\. Any other exit code indicates the instance bootstrap failed\.

 You can specify different scripts for the head node and for each queue, in the [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`CustomActions`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-CustomActions) configuration sections\.

**Note**  
Before AWS ParallelCluster version 3\.0, it was not possible to specify different scripts for head and compute nodes\. Please refer to [Moving from AWS ParallelCluster 2\.x to 3\.x](moving-from-v2-to-v3.md)\.

**Configuration**

The following configuration settings are used to define [`HeadNode`](HeadNode-v3.md) / [`CustomActions`](HeadNode-v3.md#HeadNode-v3-CustomActions) / [`OnNodeStart`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeStart) & [`OnNodeConfigured`](HeadNode-v3.md#yaml-HeadNode-CustomActions-OnNodeConfigured) and [`Scheduling`](Scheduling-v3.md) / [`CustomActions`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-CustomActions) / [`OnNodeStart`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeStart) & [`OnNodeConfigured`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomActions-OnNodeConfigured) actions and arguments\.

```
HeadNode:
  [...]
  CustomActions:
    OnNodeStart:
      # Script URL. This is run before any of the bootstrap scripts are run
      Script: s3://bucket-name/on-node-start.sh
      Args:
        - arg1
        - arg2
    OnNodeConfigured:
      # Script URL. This is run after all the bootstrap scripts are run
      Script: s3://bucket-name/on-node-configured.sh
      Args:
        - arg1
        - arg2
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
            - arg2
        OnNodeConfigured:
          Script: s3://bucket-name/on-node-configured.sh
          Args:
            - arg1
            - arg2
      Iam:
        S3Access:
          - BucketName: bucket_name
            EnableWriteAccess: false
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