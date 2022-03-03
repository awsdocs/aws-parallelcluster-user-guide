# `Scheduling` section<a name="Scheduling-v3"></a>

**\(Required\)** Defines the job scheduler to be used in the cluster along with characteristics of the compute instances managed by the job scheduler\. The two job schedulers that can be used are Slurm and AWS Batch, each of which support distinct settings and properties\.

**Topics**
+ [`Scheduling` Properties](#Scheduling-v3.properties)
+ [`AwsBatchQueues`](#Scheduling-v3-AwsBatchQueues)
+ [`SlurmQueues`](#Scheduling-v3-SlurmQueues)
+ [`SlurmSettings`](#Scheduling-v3-SlurmSettings)

```
Scheduling:
  Scheduler: slurm
  SlurmSettings:
    ScaledownIdletime: integer
    Dns:
      DisableManagedDns: boolean
      HostedZoneId: string
      UseEc2Hostnames: string
  SlurmQueues:
    - Name: string
      ComputeSettings:
        LocalStorage:
          RootVolume:
            Size: integer
            Encrypted: boolean
            VolumeType: string
            Iops: integer
            Throughput: string
          EphemeralVolume:
            MountDir: string
      CapacityType: string
      Networking:
        SubnetIds:
          - string
        AssignPublicIp: boolean
        SecurityGroups:
          - string
        AdditionalSecurityGroups:
          - string
        PlacementGroup:
          Enabled: boolean
          Id: string
        Proxy:
          HttpProxyAddress: string
      ComputeResources:
        - Name: string
          InstanceType: string
          MinCount: integer
          MaxCount: integer
          SpotPrice: float
          DisableSimultaneousMultithreading: boolean
          Efa:
            Enabled: boolean
            GdrSupport: boolean
      CustomActions:
        OnNodeStart:
          Script: string
          Args:
            - string
        OnNodeConfigured:
          Script: string
          Args:
            - string
      Iam:
        InstanceProfile: string
        InstanceRole: string
        S3Access:
          - BucketName: string
            EnableWriteAccess: boolean
            KeyName: boolean
        AdditionalIamPolicies:
          - Policy: string
      Image:
        CustomAmi: string
```

```
Scheduling:
  Scheduler: awsbatch
  AwsBatchQueues:
    - Name: string
      CapacityType: string
      Networking:
        SubnetIds:
          - string
        AssignPublicIp: boolean
        SecurityGroups:
          - string
        AdditionalSecurityGroups:
          - string
      ComputeResources:  # this maps to a Batch compute environment (initially we support only 1)
        - Name: string
          InstanceTypes:
            - string
          MinvCpus: integer
          DesiredvCpus: integer
          MaxvCpus: integer
          SpotBidPercentage: float
```

## `Scheduling` Properties<a name="Scheduling-v3.properties"></a>

`Scheduler` \(**Required**, `String`\)  
Specifies the scheduler used\. Supported values are `slurm` and `awsbatch`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `AwsBatchQueues`<a name="Scheduling-v3-AwsBatchQueues"></a>

**\(Optional\)** Settings for the AWS Batch queue\. Only one queue is supported\. This section is required when `Scheduler` is `awsbatch`\.

```
AwsBatchQueues:
  - Name: string
    CapacityType: string
    Networking:
      SubnetIds:
        - string
      AssignPublicIp: boolean
      SecurityGroups:
        - string
      AdditionalSecurityGroups:
        - string
    ComputeResources:  # this maps to a Batch compute environment (initially we support only 1)
      - Name: string
        InstanceTypes:
          - string
        MinvCpus: integer
        DesiredvCpus: integer
        MaxvCpus: integer
        SpotBidPercentage: float
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

**Topics**
+ [`AwsBatchQueues` Properties](#Scheduling-v3-AwsBatchQueues.properties)

### `AwsBatchQueues` Properties<a name="Scheduling-v3-AwsBatchQueues.properties"></a>

`Name` \(**Required**, `String`\)  
The name of the AWS Batch queue\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CapacityType` \(**Optional**, `String`\)  
The type of the compute resources used in the AWS Batch queue\. Supported values are `ONDEMAND` or `SPOT`\. The default value is `ONDEMAND`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `Networking`<a name="Scheduling-v3-AwsBatchQueues-Networking"></a>

**\(Required\)** Defines the networking configuration for the AWS Batch queue\.

```
Networking:
  SubnetIds:
    - string
  AssignPublicIp: boolean
  SecurityGroups:
    - string
  AdditionalSecurityGroups:
    - string
```

##### `Networking` Properties<a name="Scheduling-v3-AwsBatchQueues-Networking.properties"></a>

`SubnetIds` \(**Required**, `[String]`\)  
Specifies the ID of an existing subnet in which to provision the AWS Batch queue\. Currently only one subnet is supported\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`AssignPublicIp` \(**Optional**, `String`\)  
Creates or assigns a public IP address to the nodes in the AWS Batch queue\. Supported values are `true` and `false`\. The default depends on the subnet specified\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`SecurityGroups` \(**Optional**, `[String]`\)  
List of security groups to use for the AWS Batch queue\. If no security groups are specified, AWS ParallelCluster will create new ones\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`AdditionalSecurityGroups` \(**Optional**, `[String]`\)  
List of security groups to use for the AWS Batch queue\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

#### `ComputeResources`<a name="Scheduling-v3-AwsBatchQueues-ComputeResources"></a>

**\(Required\)** Defines the ComputeResources configuration for the AWS Batch queue\.

```
ComputeResources:  # this maps to a Batch compute environment (initially we support only 1)
  - Name: string
    InstanceTypes:
      - string
    MinvCpus: integer
    DesiredvCpus: integer
    MaxvCpus: integer
    SpotBidPercentage: float
```

##### `ComputeResources` Properties<a name="Scheduling-v3-AwsBatchQueues-ComputeResources.properties"></a>

`Name` \(**Required**, `String`\)  
Name for the compute environment for the AWS Batch queue\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`InstanceTypes` \(**Required**, `[String]`\)  
Array of instance types to include in this AWS Batch compute environment\. All of the instance types must use the `x86_64` architecture\.   
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`MinvCpus` \(**Optional**, `Integer`\)  
The minimum number of VCPUs for the AWS Batch compute environment\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`DesiredVcpus` \(**Optional**, `Integer`\)  
The desired number of VCPUs in the AWS Batch compute environment\. AWS Batch will adjust this value between `MinvCpus` and `MaxvCpus`, based on the demand in the job queue\.  
[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)

`MaxvCpus` \(**Optional**, `Integer`\)  
The maximum number of VCPUs for the AWS Batch compute environment\. This can not be set to a lower value than `DesiredVcpus`\.  
[Update policy: This setting can't be decreased during an update.](using-pcluster-update-cluster-v3.md#update-policy-no-decrease-v3)

`SpotBidPercentage` \(**Optional**, `Float`\)  
The maximum percentage that an EC2 Spot Instance price can be when compared with the On\-Demand price for that instance type before instances are launched\. The default value is `100` \(100%\)\. The supported range is `1`\-`100`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `SlurmQueues`<a name="Scheduling-v3-SlurmQueues"></a>

**\(Optional\)** Settings for the Slurm queue\. This section is required when `Scheduler` is `slurm`\.

```
SlurmQueues:
  - Name: string
    ComputeSettings:
      LocalStorage:
        RootVolume:
          Size: integer
          Encrypted: boolean
          VolumeType: string
          Iops: integer
          Throughput: string
        EphemeralVolume:
          MountDir: string
    CapacityType: string
    Networking:
      SubnetIds:
        - string
      AssignPublicIp: boolean
      SecurityGroups:
        - string
      AdditionalSecurityGroups:
        - string
      PlacementGroup:
        Enabled: boolean
        Id: string
      Proxy:
        HttpProxyAddress: string
    ComputeResources:
      - Name: string
        InstanceType: string
        MinCount: integer
        MaxCount: integer
        SpotPrice: float
        DisableSimultaneousMultithreading: boolean
        Efa:
          Enabled: boolean
          GdrSupport: boolean
    CustomActions:
      OnNodeStart:
        Script: string
        Args:
          - string
      OnNodeConfigured:
        Script: string
        Args:
          - string
    Iam:
      InstanceProfile: string
      InstanceRole: string
      S3Access:
        - BucketName: string
          EnableWriteAccess: boolean
          KeyName: boolean
      AdditionalIamPolicies:
        - Policy: string
    Image:
      CustomAmi: string
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

**Topics**
+ [`SlurmQueues` Properties](#Scheduling-v3-SlurmQueues.properties)

### `SlurmQueues` Properties<a name="Scheduling-v3-SlurmQueues.properties"></a>

`Name` \(**Required**, `String`\)  
The name of the Slurm queue\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CapacityType` \(**Optional**, `String`\)  
The type of the compute resources used in the Slurm queue\. Supported values are `ONDEMAND` or `SPOT`\. The default value is `ONDEMAND`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `Networking`<a name="Scheduling-v3-SlurmQueues-Networking"></a>

**\(Required\)** Defines the networking configuration for the Slurm queue\.

```
Networking:
  SubnetIds:
    - string
  AssignPublicIp: boolean
  SecurityGroups:
    - string
  AdditionalSecurityGroups:
    - string
  PlacementGroup:
    Enabled: boolean
    Id: string
  Proxy:
    HttpProxyAddress: string
```

##### `Networking` Properties<a name="Scheduling-v3-SlurmQueues-Networking.properties"></a>

`SubnetIds` \(**Required**, `[String]`\)  
Specifies the IDs of existing subnets in which to provision the Slurm queue\. Currently only one subnet is supported\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`AssignPublicIp` \(**Optional**, `String`\)  
Creates or assigns a public IP address to the nodes in the Slurm queue\. Supported values are `true` and `false`\. The default depends on the subnet specified; a subnet with public IPs will default to assigning public IP addresses\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`SecurityGroups` \(**Optional**, `[String]`\)  
List of security groups to use for the Slurm queue\. If no security groups are specified, AWS ParallelCluster will create new ones\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`AdditionalSecurityGroups` \(**Optional**, `[String]`\)  
List of additional security groups to use for the Slurm queue\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`PlacementGroup` \(**Optional**\)  
Specifies the placement group settings for the Slurm queue\.  

```
PlacementGroup:
  Enabled: boolean
  Id: string
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)    
`Enabled` \(**Optional**, `Boolean`\)  
Indicates whether a placement group is used for the Slurm queue\. If this is not specified, the default value is `false`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`Id` \(**Optional**, `String`\)  
The placement group name for an existing cluster placement group used for the Slurm queue\. If this is not specified, AWS ParallelCluster will create a new cluster placement group for each queue\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`Proxy` \(**Optional**\)  
Specifies the proxy settings for the Slurm queue\.  

```
Proxy:
  HttpProxyAddress: string
```
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)    
`HttpProxyAddress` \(**Optional**, `String`\)  
Defines an HTTP or HTTPS proxy server for the Slurm queue, typically `https://x.x.x.x:8080`\.  
There is no default value\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `Image`<a name="Scheduling-v3-SlurmQueues-Image"></a>

**\(Optional\)** Specifies the image to use for the Slurm queue\. To use the same AMI for all nodes, use the [CustomAmi](Image-v3.md#yaml-Image-CustomAmi) setting in the [`Image` section](Image-v3.md)\.

```
Image:
  CustomAmi: string
```

##### `Image` Properties<a name="Scheduling-v3-SlurmQueues-Image.properties"></a>

`CustomAmi` \(**Optional**, `String`\)  
The AMI to use for the Slurm queue instead of the default [published AMIs](https://github.com/aws/aws-parallelcluster/blob/v2.11.5/amis.txt)\.

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `ComputeResources`<a name="Scheduling-v3-SlurmQueues-ComputeResources"></a>

**\(Required\)** Defines the `ComputeResources` configuration for the Slurm queue\.

```
ComputeResources:
  - Name: string
    InstanceType: string
    MinCount: integer
    MaxCount: integer
    SpotPrice: float
    DisableSimultaneousMultithreading: boolean
    Efa:
      Enabled: boolean
      GdrSupport: boolean
```

##### `ComputeResources` Properties<a name="Scheduling-v3-SlurmQueues-ComputeResources.properties"></a>

`Name` \(**Required**, `String`\)  
Name for the compute environment for the Slurm queue\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`DisableSimultaneousMultithreading` \(**Optional**, `Boolean`\)  
If `true`, disables hyperthreading on the nodes in the Slurm queue\. The default value is `false`\.  
Not all instance types can disable hyperthreading\. For a list of instance types that support disabling hyperthreading, see [CPU cores and threads for each CPU core per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-optimize-cpu.html#cpu-options-supported-instances-values) in the *Amazon EC2 User Guide for Linux Instances*\.   
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`InstanceType` \(**Required**, `String`\)  
The instance type to use in this Slurm compute resource\. All of the instance types in a cluster must use the same processor architecture, either `x86_64` or `arm64`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`MinCount` \(**Optional**, `Integer`\)  
The minimum number of instances for the Slurm compute resource\. The default is 0\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`MaxCount` \(**Optional**, `Integer`\)  
The maximum number of instances for the Slurm compute resource\. The default is 10\.  
[Update policy: Reducing the size of a queue below the current number of nodes requires that the compute fleet be stopped first.](using-pcluster-update-cluster-v3.md#update-policy-max-count-v3)

`SpotPrice` \(**Optional**, `Float`\)  
The maximum price that will be paid for an EC2 Spot Instance before instances are launched\. The default value is the On\-Demand price\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`Efa` \(**Optional**\)  
Specifies the Elastic Fabric Adapter \(EFA\) settings for the nodes in the Slurm queue\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  

```
Efa:
  Enabled: boolean
  GdrSupport: boolean
```  
`Enabled` \(**Optional**, `Boolean`\)  
Specifies that Elastic Fabric Adapter \(EFA\) is enabled\. EFA is supported by specific instance types \(`c5n.18xlarge`, `c5n.metal`, `g4dn.metal`, `i3en.24xlarge`, `i3en.metal`, `m5dn.24xlarge`, `m5n.24xlarge`, `m5zn.12xlarge`, `m5zn.metal`, `r5dn.24xlarge`, `r5n.24xlarge`, `p3dn.24xlarge`, and `p4d.24xlarge` for x86\-64 instances and `c6gn.16xlarge` for Arm\-based Graviton2 instances\) on specific operating systems `alinux2`, `centos7`, `ubuntu1804`, or `ubuntu2004` for x86\-64 instances and `alinux2`, `ubuntu1804`, or `ubuntu2004` for Arm\-based Graviton2 instances\)\. For more information, see [Elastic Fabric Adapter](efa.md)\. A cluster placement group should be used to minimize latencies between instances\. The default value is false\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`GdrSupport` \(**Optional**, `Boolean`\)  
**\(Optional\)** Starting with AWS ParallelCluster version 3\.0\.2, this setting has no effect\. Elastic Fabric Adapter \(EFA\) support for GPUDirect RDMA \(remote direct memory access\) is always enabled if it's supported by the instance type for the Slurm compute resource and the operating system\.  
AWS ParallelCluster version 3\.0\.0 through 3\.0\.1: Specifies that support for GPUDirect RDMA is enabled for the Slurm compute resource\. Support for GPUDirect RDMA is supported by specific instance types \(`p4d.24xlarge`\) on specific operating systems \([`Os`](Image-v3.md#yaml-Image-Os) is `alinux2`, `centos7`, `ubuntu1804`, or `ubuntu2004`\)\. The default value is false\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `ComputeSettings`<a name="Scheduling-v3-SlurmQueues-ComputeSettings"></a>

**\(Required\)** Defines the `ComputeSettings` configuration for the Slurm queue\.

##### `ComputeSettings` Properties<a name="Scheduling-v3-SlurmQueues-ComputeSettings.properties"></a>

Specifies the properties of `ComputeSettings` of the nodes in the Slurm queue\.

```
ComputeSettings:
  LocalStorage:
    RootVolume:
      Size: integer
      Encrypted: boolean
      VolumeType: string
      Iops: integer
      Throughput: string
     EphemeralVolume:
      MountDir: string
```

`LocalStorage` \(**Optional**\)  
Specifies the properties of `LocalStorage` of the nodes in the Slurm queue\.  

```
LocalStorage:
  RootVolume:
    Size: integer
    Encrypted: boolean
    VolumeType: string
    Iops: integer
    Throughput: string
  EphemeralVolume:
    MountDir: string
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)    
`RootVolume` \(**Optional**\)  
Specifies the details of the root volume of the nodes in the Slurm queue\.  

```
RootVolume:
  Size: integer
  Encrypted: boolean
  VolumeType: string
  Iops: integer
  Throughput: string
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)    
`Size` \(**Optional**, `Integer`\)  
Specifies the root volume size in gibibytes \(GiB\) for the notes in the Slurm queue\. The default size comes from the AMI\. Using a different size requires that the AMI supports `growroot`\.   
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`Encrypted` \(**Optional**, `Boolean`\)  
If `true`, the root volume of the nodes in the Slurm queue are encrypted\. The default value is `false`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`VolumeType` \(**Optional**, `String`\)  
Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) of the nodes in the Slurm queue\. Supported values are `gp2`, `gp3`, `io1`, `io2`, `sc1`, `st1`, and `standard`\. The default value is `gp2`\.  
For more information, see [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`Iops` \(**Optional**, `Boolean`\)  
Defines the number of IOPS for `io1` and `io2` type volumes\.  
The default value, supported values, and `volume_iops` to `volume_size` ratio varies by `VolumeType` and `Size`\.    
`VolumeType` = `io1`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 †  
Maximum `volume_iops` to `volume_size` ratio = 50 IOPS per GiB\. 5000 IOPS requires a `volume_size` of at least 100 GiB\.  
`VolumeType` = `io2`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 \(256000 for `io2` Block Express volumes\) †  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 10 GiB\.
† Maximum IOPS is guaranteed only on [Instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS\. Older `io1` volumes might not reach full performance unless you [modify the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\. `io2` Block Express volumes support `volume_iops` values up to 256000 on `R5b` instance types\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`Throughput` \(**Optional**, `Integer`\)  
Defines the throughput for `gp3` volume types, in MiB/s\. This setting is valid only when `VolumeType` is `gp3`\. The default value is `125`\. Supported values: 125–1000 MiB/s  
The ratio of `Throughput` to `Iops` can be no more than 0\.25\. The maximum throughput of 1000 MiB/s requires that the `Iops` setting is at least 4000\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`EphemeralVolume` \(**Optional**, `Boolean`\)  
Specifies the settings for the ephemeral volume\. The ephemeral volume is created by combining all instance store volumes into a single logical volume formatted with the `ext4` file system\. The default is `/scratch`\. If the instance type does not have any instance store volumes then no ephemeral volume will be created\. For more information, see [Instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes) in the *Amazon EC2 User Guide for Linux Instances*\.  

```
EphemeralVolume:
  MountDir: string
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)    
`MountDir` \(**Optional**, `String`\)  
Specifies the mount directory for the ephemeral volume for each note in the Slurm queue\.   
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `CustomActions`<a name="Scheduling-v3-SlurmQueues-CustomActions"></a>

**\(Optional\)** Specifies custom scripts to run on the nodes in the Slurm queue\.

```
CustomActions:
  OnNodeStart:
    Script: string
    Args:
      - string
  OnNodeConfigured:
    Script: string
    Args:
      - string
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

##### `CustomActions` Properties<a name="Scheduling-v3-SlurmQueues-CustomActions.properties"></a>

`OnNodeStart` \(**Optional**, `String`\)  
Specifies a script to run on the nodes in the Slurm queue before any node deployment bootstrap action is started\. For more information, see [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://` or `s3://`\.  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.

`OnNodeConfigured` \(**Optional**, `String`\)  
Specifies a script to run on the nodes in the Slurm queue after all of the node bootstrap actions are complete\. For more information, see [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://`or `s3://`\.  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `Iam`<a name="Scheduling-v3-SlurmQueues-Iam"></a>

**\(Optional\)** Defines optional IAM settings for the Slurm queue\.

```
Iam:
  S3Access:
    - BucketName: string
      EnableWriteAccess: boolean
      KeyName: boolean
  AdditionalIamPolicies:
    - Policy: string
  InstanceProfile: string
  InstanceRole: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

##### `Iam` Properties<a name="Scheduling-v3-SlurmQueues-Iam.properties"></a>

`InstanceProfile` \(**Optional**, `String`\)  
Specifies an instance profile to override the default instance role or instance profile for the Slurm queue\. You cannot specify both `InstanceProfile` and `InstanceRole`\. The format is `arn:${Partition}:iam::${Account}:instance-profile/${InstanceProfileName}`\.  
If this is specified, the `S3Access` and `AdditionalIamPolicies` settings are ignored\. We recommend that you use `AdditionalIamPolicies` because features added to AWS ParallelCluster often require new permissions\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`InstanceRole` \(**Optional**, `String`\)  
Specifies an instance role to override the default instance role or instance profile for the Slurm queue\. You cannot specify both `InstanceProfile` and `InstanceRole`\. The format is `arn:${Partition}:iam::${Account}:role/${RoleName}`\.  
If this is specified, the `S3Access` and `AdditionalIamPolicies` settings are ignored\. We recommend that you use `AdditionalIamPolicies` because features added to AWS ParallelCluster often require new permissions\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`S3Access` \(**Optional**\)  
Specifies a bucket for the Slurm queue\. This is used to generate policies to grant the specified access to the bucket in the Slurm queue\.  

```
S3Access:
  - BucketName: string
    EnableWriteAccess: boolean
    KeyName: boolean
```
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`BucketName` \(**Required**, `String`\)  
The name of the bucket\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`KeyName` \(**Optional**, `String`\)  
The key for the bucket\. The default value is `*`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`EnableWriteAccess` \(**Optional**, `Boolean`\)  
Indicates whether write access is enabled for the bucket\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`AdditionalIamPolicies` \(**Optional**\)  
Specifies a list of Amazon Resource Names \(ARNs\) of IAM policies for Amazon EC2\. This list is attached to the root role used for the Slurm queue in addition to the permissions required by AWS ParallelCluster\.  
An IAM policy name and its ARN are different\. Names can't be used\.If the `InstanceProfile` or `InstanceRole` setting is specified, this setting is ignored\. We recommend that you use `AdditionalIamPolicies` because `AdditionalIamPolicies` are added to the permissions that AWS ParallelCluster requires, and the `InstanceRole` must include all permissions required\. The permissions required often change from release to release as features are added\.  
There is no default value\.  

```
AdditionalIamPolicies:
  - Policy: string
```  
`Policy` \(**Required**, `[String]`\)  
List of IAM policies\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `SlurmSettings`<a name="Scheduling-v3-SlurmSettings"></a>

**\(Optional\)** Defines the settings for Slurm that apply to the entire cluster\.

```
SlurmSettings:
  ScaledownIdletime: integer
  Dns:
    DisableManagedDns: boolean
    HostedZoneId: string
    UseEc2Hostnames: string
```

**Topics**
+ [`SlurmSettings` Properties](#Scheduling-v3-SlurmSettings.properties)
+ [`Dns`](#Scheduling-v3-SlurmSettings-Dns)

### `SlurmSettings` Properties<a name="Scheduling-v3-SlurmSettings.properties"></a>

`ScaledownIdletime` \(**Optional**, `Integer`\)  
Defines the amount of time in minutes without a job, after which the Slurm node terminates\.  
The default value is `10`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

### `Dns`<a name="Scheduling-v3-SlurmSettings-Dns"></a>

**\(Optional\)** Defines the settings for Slurm that apply to the entire cluster\.

```
Dns:
  DisableManagedDns: boolean
  HostedZoneId: string
  UseEc2Hostnames: string
```

#### `Dns` Properties<a name="Scheduling-v3-SlurmSettings-Dns.properties"></a>

`DisableManagedDns` \(**Optional**, `Boolean`\)  
If `true`, the DNS entries for the cluster shouldn't be created\. By default, AWS ParallelCluster creates a Route 53 hosted zone\. The default value is `false`\. If `DisableManagedDns` is set to `true`, the hosted zone isn't created by AWS ParallelCluster\.  
A name resolution system is required for the cluster to operate properly\. If `DisableManagedDns` is set to `true`, an additional name resolution system must also be provided\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`HostedZoneId` \(**Optional**, `String`\)  
Defines a custom Route 53 hosted zone id to use for DNS name resolution for the cluster\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`UseEc2Hostnames` \(**Optional**, `String`\)  
Specifies the use of default EC2 hostnames\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)