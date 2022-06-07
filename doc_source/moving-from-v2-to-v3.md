# Moving from AWS ParallelCluster 2\.x to 3\.x<a name="moving-from-v2-to-v3"></a>

## Custom Bootstrap Actions<a name="custom_bootstrap_actions"></a>

With AWS ParallelCluster 3, you can specify different custom bootstrap actions scripts for the head node and compute nodes using `OnNodeStart` \(`pre_install` in AWS ParallelCluster version 2\) and `OnNodeConfigured` \(`post_install` in AWS ParallelCluster version 2\) parameters in the [`HeadNode`](HeadNode-v3.md) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) sections\. For more information, see [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md)\. 

 Custom bootstrap actions scripts that are developed for AWS ParallelCluster 2 must be adapted to be used in AWS ParallelCluster 3:
+ We don't recommend using `/etc/parallelcluster/cfnconfig` and `cfn_node_type` to differentiate between head and compute nodes\. Instead, we recommend that you specify two different scripts in the [`HeadNode`](HeadNode-v3.md) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues)\.
+ If you prefer to continue loading `/etc/parallelcluster/cfnconfig` for use in your bootstrap actions script, note the value of `cfn_node_type` is changed from "MasterServer" to "HeadNode" \(see: [Inclusive language](#inclusive_language)\)\.
+ On AWS ParallelCluster 2, the first input argument to bootstrap action scripts was the S3 URL to the script and was reserved\. In AWS ParallelCluster 3, only the arguments configured in the configuration are passed to the scripts\.

**Warning**  
Using internal variables provided through the `/etc/parallelcluster/cfnconfig` file isn't officially supported\. This file might be removed as part of a future release\.

## AWS ParallelCluster 2\.x and 3\.x use different configuration file syntax<a name="pcluster_use_different_config_file_syntax"></a>

 ******** 

AWS ParallelCluster 3\.x configuration uses YAML syntax\. The full reference can be found at [Configuration files](configuration-v3.md)\.

In addition to requiring a YAML file format, a number of configuration sections, settings, and parameter values have been updated in AWS ParallelCluster 3\.x\. In this section, we note key changes to the AWS ParallelCluster configuration along with side\-by\-side examples illustrating these differences across each version of AWS ParallelCluster\.

**Example of multiple scheduler queues configuration with hyperthreading enabled and disabled**

AWS ParallelCluster 2:

```
[cluster default]
queue_settings = ht-enabled, ht-disabled
...

[queue ht-enabled]
compute_resource_settings = ht-enabled-i1
disable_hyperthreading = false

[queue ht-disabled]
compute_resource_settings = ht-disabled-i1
disable_hyperthreading = true

[compute_resource ht-enabled-i1]
instance_type = c5n.18xlarge
[compute_resource ht-disabled-i1]
instance_type = c5.xlarge
```

AWS ParallelCluster 3:

```
...
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: ht-enabled
      Networking:
        SubnetIds:
          - compute_subnet_id
      ComputeResources:
        - Name: ht-enabled-i1
          DisableSimultaneousMultithreading: true
          InstanceType: c5n.18xlarge     
    - Name: ht-disabled
      Networking:
        SubnetIds:
          - compute_subnet_id
      ComputeResources:
        - Name: ht-disabled-i1
          DisableSimultaneousMultithreading: false
          InstanceType: c5.xlarge
```

**Example of new FSx for Lustre file\-system configuration**

AWS ParallelCluster 2:

```
[cluster default]
fsx_settings = fsx
...

[fsx fsx]
shared_dir = /shared-fsx
storage_capacity = 1200
imported_file_chunk_size = 1024
import_path = s3://bucket
export_path = s3://bucket/export_dir
weekly_maintenance_start_time = 3:02:30
deployment_type = PERSISTENT_1
data_compression_type = LZ4
```

AWS ParallelCluster 3:

```
...
SharedStorage:
  - Name: fsx
    MountDir: /shared-fsx
    StorageType: FsxLustre
    FsxLustreSettings:
      StorageCapacity: 1200
      ImportedFileChunkSize: 1024
      ImportPath: s3://bucket
      ExportPath: s3://bucket/export_dir
      WeeklyMaintenanceStartTime: "3:02:30"
      DeploymentType: PERSISTENT_1
      DataCompressionType: LZ4
```

**Example of a cluster configuration mounting an existing FSx for Lustre file\-system**

AWS ParallelCluster 2:

```
[cluster default]
fsx_settings = fsx
...

[fsx fsx]
shared_dir = /shared-fsx
fsx_fs_id = fsx_fs_id
```

AWS ParallelCluster 3:

```
...
SharedStorage:
  - Name: fsx
    MountDir: /shared-fsx
    StorageType: FsxLustre
    FsxLustreSettings:
      FileSystemId: fsx_fs_id
```

 **Example of a cluster with the Intel HPC Platform Specification software stack** 

AWS ParallelCluster 2:

```
[cluster default]
enable_intel_hpc_platform = true
...
```

AWS ParallelCluster 3:

```
...
AdditionalPackages:
  IntelSoftware:
    IntelHpcPlatform: true
```

 Notes: 
+  The installation of Intel HPC Platform Specification software is subject to the terms and conditions of the applicable [Intel End User License Agreement](https://software.intel.com/en-us/articles/end-user-license-agreement) 

 **Example of custom IAM configurations including: instance profile, instance role, additional policies for instances and the role for the lambda functions associated with the cluster** 

AWS ParallelCluster 2: 

```
[cluster default]
additional_iam_policies = arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess,arn:aws:iam::aws:policy/AmazonDynamoDBReadOnlyAccess
ec2_iam_role = ec2_iam_role
iam_lambda_role = lambda_iam_role
...
```

AWS ParallelCluster 3: 

```
...
Iam:
  Roles:
    CustomLambdaResources: lambda_iam_role
HeadNode:
  ...
  Iam:
    InstanceRole: ec2_iam_role
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue1
      ...
      Iam:
        InstanceProfile: iam_instance_profile
    - Name: queue2
      ...
      Iam:
        AdditionalIamPolicies:
          - Policy: arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
          - Policy: arn:aws:iam::aws:policy/AmazonDynamoDBReadOnlyAccess
```

 Notes: 
+ For AWS ParallelCluster 2 the IAM settings are applied to all the instances of a cluster and `additional_iam_policies` can't be used in conjunction with `ec2_iam_role` 
+ For AWS ParallelCluster 3, you can have different IAM settings for head and compute nodes and even specify different IAM settings for each compute queue\. 
+ For AWS ParallelCluster 3, you can use an IAM instance profile as an alternative to an IAM role\. `InstanceProfile`, `InstanceRole` or `AdditionalIamPolicies` can't be configured together\. 

**Example of custom bootstrap actions**

AWS ParallelCluster 2:

```
[cluster default]
s3_read_resource = arn:aws:s3:::bucket_name/*
pre_install = s3://bucket_name/scripts/pre_install.sh
pre_install_args = 'R curl wget'
post_install = s3://bucket_name/scripts/post_install.sh
post_install_args = "R curl wget"
...
```

AWS ParallelCluster 3: 

```
...
HeadNode:
  ...
  CustomActions:
    OnNodeStart:
      Script: s3://bucket_name/scripts/pre_install.sh
      Args:
        - R
        - curl
        - wget
    OnNodeConfigured:
      Script: s3://bucket_name/scripts/post_install.sh
      Args: ['R', 'curl', 'wget']
  Iam:
    S3Access:
      - BucketName: bucket_name
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue1
      ...
      CustomActions:
        OnNodeStart:
          Script: s3://bucket_name/scripts/pre_install.sh
          Args: ['R', 'curl', 'wget']
        OnNodeConfigured:
          Script: s3://bucket_name/scripts/post_install.sh
          Args: ['R', 'curl', 'wget']
      Iam:
        S3Access:
          - BucketName: bucket_name
```

 **Example of a cluster with read and write access to the S3 bucket resources ** 

AWS ParallelCluster 2: 

```
[cluster default]
s3_read_resource = arn:aws:s3:::bucket/read_only/*
s3_read_write_resource = arn:aws:s3:::bucket/read_and_write/*
...
```

AWS ParallelCluster 3: 

```
...
HeadNode:
  ...
  Iam:
    S3Access:
      - BucketName: bucket_name
        KeyName: read_only/
        EnableWriteAccess: False
      - BucketName: bucket_name
        KeyName: read_and_write/
        EnableWriteAccess: True
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue1
      ...
      Iam:
        S3Access:
          - BucketName: bucket_name
            KeyName: read_only/
            EnableWriteAccess: False
          - BucketName: bucket_name
            KeyName: read_and_write/
            EnableWriteAccess: True
```

## Inclusive language<a name="inclusive_language"></a>

 AWS ParallelCluster 3 uses the words "head node" in places where "master" was used in AWS ParallelCluster 2\. This includes the following: 
+  Variable exported in the AWS Batch job environment changed: from `MASTER_IP` to `PCLUSTER_HEAD_NODE_IP`\. 
+  All AWS CloudFormation outputs changed from `Master*` to `HeadNode*` 
+  All NodeType and tags changed from `Master` to `HeadNode`\. 

## Scheduler Support<a name="scheduler_support"></a>

 AWS ParallelCluster 3\.x doesn't support Son of Grid Engine \(SGE\) and Torque schedulers\.

 The AWS Batch commands `awsbhosts`, `awsbkill`, `awsbout`, `awsbqueues`, `awsbstat`, and `awsbsub` are distributed as a separate `aws-parallelcluster-awsbatch-cli` PyPI package\. This package is installed by AWS ParallelCluster on the head node\. You can still use these AWS Batch commands from the cluster's head node\. However, if you wish to use AWS Batch commands from a location other than the head node, you must first install the `aws-parallelcluster-awsbatch-cli` PyPI package\. 

## AWS ParallelCluster CLI<a name="parallelcluster_cli"></a>

The AWS ParallelCluster command line interface \(CLI\) has been changed\. The new syntax is described in [AWS ParallelCluster CLI commands](commands-v3.md)\. The output format for the CLI is a [JSON](https://json.org/) string\. 

 **Configuring a new cluster** 

The `pcluster configure` command includes different parameters in AWS ParallelCluster 3 as compared to AWS ParallelCluster 2\. For more information, see [`pcluster configure`](pcluster.configure-v3.md)\. 

Note also that the configuration file syntax has changed from AWS ParallelCluster 2\. For a full reference of the cluster configuration settings, see [Cluster configuration file](cluster-configuration-file-v3.md)\. 

 **Creating a new cluster** 

AWS ParallelCluster 2's `pcluster create` command has been replaced by the [`pcluster create-cluster`](pcluster.create-cluster-v3.md) command\. 

Note the default behavior in AWS ParallelCluster 2\.x, without the `-nw` option, is to wait on cluster creation events, while AWS ParallelCluster 3\.x command returns immediately\. The progress of the cluster creation can be monitored using [`pcluster describe-cluster`](pcluster.describe-cluster-v3.md) 

 An AWS ParallelCluster 3 configuration file contains a single cluster definition, so the `-t` parameter is no more needed\. 

 The following is an example configuration file\. 

```
# AWS ParallelCluster v2
$ pcluster create \
    -r REGION \
    -c V2_CONFIG_FILE \
    -nw \
    -t CLUSTER_TEMPLATE \
    CLUSTER_NAME

# AWS ParallelCluster v3
$ pcluster create-cluster \
    --region REGION \
    --cluster-configuration V3_CONFIG_FILE \
    --cluster-name CLUSTER_NAME
```

 **Listing clusters** 

 The `pcluster list` AWS ParallelCluster 2\.x command must be replaced with [`pcluster list-clusters`](pcluster.list-clusters-v3.md) command\. 

 Note: You need AWS ParallelCluster v2 CLI to list clusters created with 2\.x versions of AWS ParallelCluster\. See [Install AWS ParallelCluster in a virtual environment \(recommended\)](install-v3-virtual-environment.md) for how to install multiple versions of AWS ParallelCluster using virtual environments\. 

```
# AWS ParallelCluster v2
$ pcluster list -r REGION

# AWS ParallelCluster v3
$ pcluster list-clusters --region REGION
```

 **Starting and Stopping a cluster** 

 The `pcluster start` and `pcluster stop` AWS ParallelCluster 2\.x commands must be replaced with [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md) commands\. 

 Starting a compute fleet: 

```
# AWS ParallelCluster v2
$ pcluster start \
    -r REGION \
    CLUSTER_NAME

# AWS ParallelCluster v3 - Slurm fleets
$ pcluster update-compute-fleet \
    --region REGION \
    --cluster-name CLUSTER_NAME \
    --status START_REQUESTED

# AWS ParallelCluster v3 - AWS Batch fleets
$ pcluster update-compute-fleet \
    --region REGION \
    --cluster-name CLUSTER_NAME \
    --status ENABLED
```

 Stopping a compute fleet: 

```
# AWS ParallelCluster v2
$ pcluster stop \
    -r REGION \
    CLUSTER_NAME

# AWS ParallelCluster v3 - Slurm fleets
$ pcluster update-compute-fleet \
    --region REGION \
    --cluster-name CLUSTER_NAME \
    --status STOP_REQUESTED

# AWS ParallelCluster v3 - AWS Batch fleets
$ pcluster update-compute-fleet \
    --region REGION \
    --cluster-name CLUSTER_NAME \
    --status DISABLED
```

 **Connecting to a cluster** 

 The `pcluster ssh` AWS ParallelCluster 2\.x command has different parameters names in AWS ParallelCluster 3\.x\. See [`pcluster ssh`](pcluster.ssh-v3.md) 

 Connecting to a cluster: 

```
# AWS ParallelCluster v2
$ pcluster ssh \
    -r REGION \
    CLUSTER_NAME \
    -i ~/.ssh/id_rsa

# AWS ParallelCluster v3
$ pcluster ssh \
    --region REGION \
    --cluster-name CLUSTER_NAME \
    -i ~/.ssh/id_rsa
```

## IMDS configuration update<a name="imds-update"></a>

Starting with version 3\.0\.0, AWS ParallelCluster introduced support for restricting access to the head node’s IMDS \(and the instance profile credentials\) to a subset of superusers, by default\. For more information, see [`Imds` Properties](HeadNode-v3.md#HeadNode-v3-Imds.properties)\.