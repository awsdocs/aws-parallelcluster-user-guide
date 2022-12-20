# `Scheduling` section<a name="Scheduling-v3"></a>

**\(Required\)**Defines the job scheduler to be used in the cluster along with characteristics of the compute instances managed by the job scheduler\. The two job schedulers that can be used are Slurm and AWS Batch, each of which support distinct settings and properties\.

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
    QueueUpdateStrategy: string
    EnableMemoryBasedScheduling: integer
    Database:
      Uri: string
      UserName: string
      PasswordSecretArn: string
    Dns:
      DisableManagedDns: boolean
      HostedZoneId: string
      UseEc2Hostnames: boolean  
  SlurmQueues:
    - Name: string  
      ComputeSettings:
        LocalStorage:
          RootVolume:
            Size: integer
            Encrypted: boolean
            VolumeType: string
            Iops: integer
            Throughput: integer
          EphemeralVolume:
            MountDir: string
      CapacityReservationTarget:
        CapacityReservationId: string
        CapacityReservationResourceGroupArn: string
      CapacityType: string
      AllocationStrategy: string
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
          Name: string
        Proxy:
          HttpProxyAddress: string
      ComputeResources:
        - Name: string
          InstanceType: string
          Instances:
            - InstanceType: string
          MinCount: integer
          MaxCount: integer
          SpotPrice: float
          DisableSimultaneousMultithreading: boolean
          SchedulableMemory: integer
          Efa:
            Enabled: boolean
            GdrSupport: boolean          
          CapacityReservationTarget:
            CapacityReservationId: string
            CapacityReservationResourceGroupArn: string
          Networking:   
            PlacementGroup:
              Enabled: boolean
              Name: string
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
            KeyName: string
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

**\(Optional\)** Settings for the AWS Batch queue\. Only one queue is supported\. This section is required when [`Scheduler`](#yaml-Scheduling-Scheduler) is `awsbatch`\. For more information about the `awsbatch` scheduler, see [networking setup](network-configuration-v3.md#network-configuration-v3-batch) and [AWS Batch \(`awsbatch`\)](awsbatchcli-v3.md)\.

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

### `AwsBatchQueues` Properties<a name="Scheduling-v3-AwsBatchQueues.properties"></a>

`Name` \(**Required**, `String`\)  
The name of the AWS Batch queue\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CapacityType` \(**Optional**, `String`\)  
The type of the compute resources used in the AWS Batch queue\. Supported values are `ONDEMAND` or `SPOT`\. The default value is `ONDEMAND`\.  
When `CapacityType` is set to `SPOT`, there must be an `AWSServiceRoleForEC2Spot` service\-linked role in your account\. You can create this role with the following AWS CLI command:  

```
$ aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
```
For more information, see [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.
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

**\(Optional\)** Settings for the Slurm queue\. This section is required when [`Scheduler`](#yaml-Scheduling-Scheduler) is `slurm`\.

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
          Throughput: integer
        EphemeralVolume:
          MountDir: string
     CapacityReservationTarget:
       CapacityReservationId: string
       CapacityReservationResourceGroupArn: string
    CapacityType: string
    AllocationStrategy: string
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
        Name: string
     Proxy:
        HttpProxyAddress: string
    ComputeResources:
      - Name: string
        InstanceType: string
        Instances:
          - InstanceType: string        
        MinCount: integer
        MaxCount: integer
        SpotPrice: float
        DisableSimultaneousMultithreading: boolean
        SchedulableMemory: integer
        Efa:
          Enabled: boolean
          GdrSupport: boolean    
        CapacityReservationTarget:
          CapacityReservationId: string
          CapacityReservationResourceGroupArn: string     
        Networking:   
          PlacementGroup:
            Enabled: boolean
            Name: string
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
          KeyName: string
      AdditionalIamPolicies:
        - Policy: string
    Image:
      CustomAmi: string
```

[Update policy: For this list values setting, a new value can be added during an update or the compute fleet must be stopped when removing an existing value.](using-pcluster-update-cluster-v3.md#update-policy-list-values-v3)

### `SlurmQueues` Properties<a name="Scheduling-v3-SlurmQueues.properties"></a>

`Name` \(**Required**, `String`\)  
The name of the Slurm queue\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CapacityReservationTarget`  

```
CapacityReservationTarget:
   CapacityReservationId: string
   CapacityReservationResourceGroupArn: string
```
Specifies the on\-demand capacity reservation to use for the queue's compute resources\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.    
`CapacityReservationId` \(**Optional**, `String`\)  
Indicates the ID of the existing capacity reservation to target for the queue's compute resources\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)  
`CapacityReservationResourceGroupArn` \(**Optional**, `String`\)  
Indicates the Amazon Resource Name \(ARN\) of the resource group that serves as the service linked group of capacity reservations for the queue's compute resources\. AWS ParallelCluster identifies and uses the most appropriate capacity reservation from the group for the compute resource:  
+ If `PlacementGroup` is enabled in [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking) or [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking), AWS ParallelCluster selects a resource group that targets the instance type and `PlacementGroup` for a compute resource if it exists\.

  The `PlacementGroup` must target one of the instances types defined in [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources)\.
+ If `PlacementGroup` isn't enabled in [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking) or [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking), AWS ParallelCluster selects a resource group that targets only the instance type of a compute resource, if it exists\.
The resource group must have at least one ODCR for each instance type across all the queue's compute resources\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`CapacityReservationTarget` is added with AWS ParallelCluster version 3\.3\.0\.

`CapacityType` \(**Optional**, `String`\)  
The type of the compute resources used in the Slurm queue\. Supported values are `ONDEMAND` or `SPOT`\. The default value is `ONDEMAND`\.  
When `CapacityType` is set to `SPOT`, there must be an `AWSServiceRoleForEC2Spot` service\-linked role in your account\. You can create this role with the following AWS CLI command:  

```
$ aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
```
For more information, see [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`AllocationStrategy` \(**Optional**, `String`\)  
Specify the allocation strategy for all the compute resources defined with [`Instances`](#yaml-Scheduling-SlurmQueues-ComputeResources-Instances)\.  
Valid values: `lowest-price` \| `capacity-optimized`  
Default: `lowest-price`    
`lowest-price`  
+ When using `CapacityType = ONDEMAND`, EC2 Fleet uses price to determine the order and launches the lowest price instances first\.
+ When using `CapacityType = SPOT`, EC2 Fleet launches instances from the lowest price Spot Instance pool that has available capacity\. If a pool runs out of capacity before fulfilling your required capacity, EC2 Fleet fulfills your request by launching instances from the lowest price Spot Instance pool that has available capacity\. EC2 Fleet might launch Spot Instances from several different pools\.  
`capacity-optimized`  
+ Not available when using `CapacityType = ONDEMAND`\.
+ When using `CapacityType = SPOT`, EC2 Fleet launches instances from Spot Instance pools with optimal capacity for the number of instances to be launched\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`AllocationStrategy` is supported starting with AWS ParallelCluster version 3\.3\.0\.

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
    Name: string
  Proxy:
    HttpProxyAddress: string
```

[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

##### `Networking` Properties<a name="Scheduling-v3-SlurmQueues-Networking.properties"></a>

`SubnetIds` \(**Required**, `[String]`\)  
Specifies the IDs of existing subnets in which to provision the Slurm queue\. Currently only one subnet is supported\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`AssignPublicIp` \(**Optional**, `String`\)  
Creates or assigns a public IP address to the nodes in the Slurm queue\. Supported values are `true` and `false`\. The default depends on the subnet specified; a subnet with public IPs will default to assigning public IP addresses\.  
If you define a p4d, an hpc6id or another instance type that has multiple network interfaces or a network interface card, you must set [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`ElasticIp`](HeadNode-v3.md#yaml-HeadNode-Networking-ElasticIp) to `true` to provide public access\. AWS public IPs can only be assigned to instances launched with a single network interface\. For this case, we recommend that you use a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) to provide public access to the cluster compute nodes\. In this case, set `AssignPublicIp` to `false`\. For more information on IP addresses, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`SecurityGroups` \(**Optional**, `[String]`\)  
List of security groups to use for the Slurm queue\. If no security groups are specified, AWS ParallelCluster will create new ones\.  
Verify that the security groups are configured correctly for your [SharedStorage](SharedStorage-v3.md) systems\.  
If you're enabling [Efa](#yaml-Scheduling-SlurmQueues-ComputeResources-Efa) for your compute instances, ensure that your EFA\-enabled instances are members of a security group that allows all inbound and outbound traffic to itself\.
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
  Name: string
```
[Update policy: All compute nodes must be stopped for a managed placement group deletion. The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-remove-placement-group-v3)    
`Enabled` \(**Optional**, `Boolean`\)  
Indicates whether a placement group is used for the Slurm queue\. The default is `false`\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Id` \(**Optional**, `String`\)  
The placement group name for an existing cluster placement group that's used for the Slurm queue\. Make sure you provide the placement group *name* and *not the ID*\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Name` \(**Optional**, `String`\)  
The placement group name for an existing cluster placement group that's used for the Slurm queue\. Make sure you provide the placement group *name* and *not the ID*\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)
+ If `PlacementGroup` / `Enabled` is set to `true`, without a `Name` or `Id` defined, each compute resource is assigned its own managed placement group, unless [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking) / [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking-PlacementGroup) is defined to override this setting\.
+ Starting with AWS ParallelCluster version 3\.3\.0, [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#Scheduling-v3-SlurmQueues-Networking) / [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Name`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Name) was added as a preferred alternative to [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#Scheduling-v3-SlurmQueues-Networking) / [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Id`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Id)\.

  [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Id`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Id) and [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Name`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Name) are equivalent and you can use either one\.

   If you include both [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Id`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Id) and [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Name`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Name), AWS ParallelCluster fails\.

  You don't need to update your cluster to use [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) / [`Name`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup-Name)\.

`Proxy` \(**Optional**\)  
Specifies the proxy settings for the Slurm queue\.  

```
Proxy:
  HttpProxyAddress: string
```
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)    
`HttpProxyAddress` \(**Optional**, `String`\)  
Defines an HTTP or HTTPS proxy server for the Slurm queue, typically `https://x.x.x.x:8080`\.  
There is no default value\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

#### `Image`<a name="Scheduling-v3-SlurmQueues-Image"></a>

**\(Optional\)** Specifies the image to use for the Slurm queue\. To use the same AMI for all nodes, use the [CustomAmi](Image-v3.md#yaml-Image-CustomAmi) setting in the [`Image` section](Image-v3.md)\.

```
Image:
  CustomAmi: string
```

[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

##### `Image` Properties<a name="Scheduling-v3-SlurmQueues-Image.properties"></a>

`CustomAmi` \(**Optional**, `String`\)  
The AMI to use for the Slurm queue instead of the default AMIs\. You can use the pcluster CLI command to view a list of the default AMIs\.  

```
pcluster list-official-images
```
If the custom AMI requires additional permissions for its launch, these permissions must be added to the head node policy\.  
For example, if a custom AMI has an encrypted snapshot associated with it, the following additional policies are required in the head node policies:  

```
{
   "Version": "2012-10-17",
   "Statement": [
       {
           "Effect": "Allow",
           "Action": [
               "kms:DescribeKey",
               "kms:ReEncrypt*",
               "kms:CreateGrant",
               "kms:Decrypt"
           ],
           "Resource": [
               "arn:aws:kms:<AWS_REGION>:<AWS_ACCOUNT_ID>:key/<AWS_KMS_KEY_ID>"
           ]                                                    
       }
   ]
}
```
To troubleshoot custom AMI validation warnings, see [Troubleshooting custom AMI issues](troubleshooting-v3.md#troubleshooting-v3-custom-amis)\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

#### `ComputeResources`<a name="Scheduling-v3-SlurmQueues-ComputeResources"></a>

**\(Required\)** Defines the `ComputeResources` configuration for the Slurm queue\.

```
ComputeResources:
  - Name: string
    InstanceType: string
    Instances:
      - InstanceType: string    
    MinCount: integer
    MaxCount: integer
    SpotPrice: float
    DisableSimultaneousMultithreading: boolean
    SchedulableMemory: integer
    Efa:
      Enabled: boolean
      GdrSupport: boolean
    CapacityReservationTarget:
      CapacityReservationId: string
      CapacityReservationResourceGroupArn: string
    Networking:   
      PlacementGroup:
        Enabled: boolean
        Name: string
```

[Update policy: For this list values setting, a new value can be added during an update or the compute fleet must be stopped when removing an existing value.](using-pcluster-update-cluster-v3.md#update-policy-list-values-v3)

##### `ComputeResources` Properties<a name="Scheduling-v3-SlurmQueues-ComputeResources.properties"></a>

`Name` \(**Required**, `String`\)  
Name for the compute environment for the Slurm queue\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`InstanceType` \(**Required**, `String`\)  
The instance type to use in this Slurm compute resource\. All of the instance types in a cluster must use the same processor architecture, either `x86_64` or `arm64`\.  
The cluster configuration must define either [InstanceType](#yaml-Scheduling-SlurmQueues-ComputeResources-InstanceType) or [Instances](#yaml-Scheduling-SlurmQueues-ComputeResources-Instances)\. AWS ParallelCluster fails if both are defined\.  
If you define a p4d, an hpc6id or another instance type that has multiple network interfaces or a network interface card, you must launch the compute instances in private subnet as describe in [AWS ParallelCluster using two subnets](network-configuration-v3.md#network-configuration-v3-two-subnets)\. AWS public IPs can only be assigned to instances launched with a single network interface\. For more information, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)\.

`Instances` \(**Required**\)  
Specifies the list of instance types for a compute resource\. To specify the allocation strategy for the list of instance types, see [`AllocationStrategy`](#yaml-Scheduling-SlurmQueues-AllocationStrategy)\.  
The cluster configuration must define either [`InstanceType`](#yaml-Scheduling-SlurmQueues-ComputeResources-InstanceType) or [`Instances`](#yaml-Scheduling-SlurmQueues-ComputeResources-Instances)\. AWS ParallelCluster fails if both are defined\.  
For more information, see [Multiple instance type allocation with Slurm](slurm-multiple-instance-allocation-v3.md)\.  

```
`Instances`:
   - `InstanceType`: string
```
[Update policy: For this list values setting, a new value can be added during an update or the compute fleet must be stopped when removing an existing value.](using-pcluster-update-cluster-v3.md#update-policy-list-values-v3)    
`InstanceType` \(**Required**, `String`\)  
The instance type to use in this Slurm compute resource\. All of the instance types in a cluster must use the same processor architecture, either `x86_64` or `arm64`\.  
The instance types listed in [`Instances`](#yaml-Scheduling-SlurmQueues-ComputeResources-Instances) must have:  
+ The same number of vCPUs or if [`DisableSimultaneousMultithreading`](#yaml-Scheduling-SlurmQueues-ComputeResources-DisableSimultaneousMultithreading) is set to `true`, the same number of cores\.
+ The same number of accelerators of the same manufacturers\.
+ EFA supported, if [`Efa`](#yaml-Scheduling-SlurmQueues-ComputeResources-Efa) / [`Enabled`](#yaml-Scheduling-SlurmQueues-ComputeResources-Efa-Enabled) set to `true`\.
The instance types listed in [`Instances`](#yaml-Scheduling-SlurmQueues-ComputeResources-Instances) can have:  
+ Different amount of memory\.

  In this case, the minimum memory is to be set as a consumable Slurm resource\. [`EnableMemoryBasedScheduling`](#yaml-Scheduling-SlurmSettings-EnableMemoryBasedScheduling) can't be enabled for multiple instance types\.
+ Different network cards\.

  In this case, the number of network interfaces configured for the compute resource is defined by the instance type with the smallest number of network cards\.
+ Different network bandwitdh\.
+ Different instance store size\.
If you define a p4d, an hpc6id or another instance type that has multiple network interfaces or a network interface card, you must launch the compute instances in private subnet as describe in [AWS ParallelCluster using two subnets](network-configuration-v3.md#network-configuration-v3-two-subnets)\. AWS public IPs can only be assigned to instances launched with a single network interface\. For more information, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)
`Instances` is supported starting with AWS ParallelCluster version 3\.3\.0\.

`MinCount` \(**Optional**, `Integer`\)  
The minimum number of instances for the Slurm compute resource\. The default is 0\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`MaxCount` \(**Optional**, `Integer`\)  
The maximum number of instances for the Slurm compute resource\. The default is 10\.  
[Update policy: Reducing the size of a queue below the current number of nodes requires that the compute fleet be stopped first.](using-pcluster-update-cluster-v3.md#update-policy-max-count-v3)

`SpotPrice` \(**Optional**, `Float`\)  
The maximum price that will be paid for an EC2 Spot Instance before instances are launched\. The default value is the On\-Demand price\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`DisableSimultaneousMultithreading` \(**Optional**, `Boolean`\)  
If `true`, disables multithreading on the nodes in the Slurm queue\. The default value is `false`\.  
Not all instance types can disable multithreading\. For a list of instance types that support disabling multithreading, see [CPU cores and threads for each CPU core per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-optimize-cpu.html#cpu-options-supported-instances-values) in the *Amazon EC2 User Guide for Linux Instances*\.   
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`SchedulableMemory` \(**Optional**, `Integer`\)  
The amount of memory in MiB configured in the Slurm parameter `RealMemory` for the compute nodes of a compute resource\. This value is the upper limit for the node memory available to jobs when [`SlurmSettings`](#Scheduling-v3-SlurmSettings) / [`EnableMemoryBasedScheduling`](#yaml-Scheduling-SlurmSettings-EnableMemoryBasedScheduling) is enabled\. The default value is 95 percent of the memory listed in [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types) and returned by the Amazon EC2 API [DescribeInstanceTypes](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInstanceTypes.html)\. Be sure to convert values given in GiB to MiB\.  
Supported values: `1-EC2Memory`  
`EC2Memory` is the memory \(in MiB\) that's listed in [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types) and returned by the Amazon EC2 API [DescribeInstanceTypes](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInstanceTypes.html)\. Be sure to convert values given in GiB to MiB\.  
This option is most relevant when [`SlurmSettings`](#Scheduling-v3-SlurmSettings) / [`EnableMemoryBasedScheduling`](#yaml-Scheduling-SlurmSettings-EnableMemoryBasedScheduling) is enabled\. For more information, see [Slurm memory\-based scheduling](slurm-mem-based-scheduling-v3.md)\.  
`SchedulableMemory` is supported starting with AWS ParallelCluster version 3\.2\.0\.  
Starting with version 3\.2\.0, by default, AWS ParallelCluster configures `RealMemory` for Slurm compute nodes to 95 percent of the memory returned by the Amazon EC2 API `DescribeInstanceTypes`\. This configuration is independent of the value of `EnableMemoryBasedScheduling`\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`Efa` \(**Optional**\)  
Specifies the Elastic Fabric Adapter \(EFA\) settings for the nodes in the Slurm queue\.  

```
Efa:
  Enabled: boolean
  GdrSupport: boolean
```
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)    
`Enabled` \(**Optional**, `Boolean`\)  
Specifies that Elastic Fabric Adapter \(EFA\) is enabled\. To view the list of EC2 instances that support EFA, see [Supported instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html#efa-instance-types) in the *Amazon EC2 User Guide for Linux Instances*\. For more information, see [Elastic Fabric Adapter](efa-v3.md)\. We recommend that you use a cluster [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#Scheduling-v3-SlurmQueues-Networking) / [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) to minimize latencies between instances\.  
The default value is `false`\.  
If you're defining a custom security group in [SecurityGroups](#yaml-Scheduling-SlurmQueues-Networking-SecurityGroups), ensure that your EFA\-enabled instances are members of a security group that allows all inbound and outbound traffic to itself\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`GdrSupport` \(**Optional**, `Boolean`\)  
**\(Optional\)** Starting with AWS ParallelCluster version 3\.0\.2, this setting has no effect\. Elastic Fabric Adapter \(EFA\) support for GPUDirect RDMA \(remote direct memory access\) is always enabled if it's supported by the instance type for the Slurm compute resource and the operating system\.  
AWS ParallelCluster version 3\.0\.0 through 3\.0\.1: Specifies that support for GPUDirect RDMA is enabled for the Slurm compute resource\. Support for GPUDirect RDMA is supported by specific instance types \(`p4d.24xlarge`\) on specific operating systems \([`Os`](Image-v3.md#yaml-Image-Os) is `alinux2`, `centos7`, `ubuntu1804`, or `ubuntu2004`\)\. The default value is false\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`CapacityReservationTarget`  

```
CapacityReservationTarget:
   CapacityReservationId: string
   CapacityReservationResourceGroupArn: string
```
Specifies the on\-demand capacity reservation to use for the compute resource\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.    
`CapacityReservationId` \(**Optional**, `String`\)  
Indicates the ID of the existing capacity reservation to target for the compute resource\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.  
`CapacityReservationResourceGroupArn` \(**Optional**, `String`\)  
Indicates the Amazon Resource Name \(ARN\) of the resource group that serves as the service linked group of capacity reservations for the compute resource\. AWS ParallelCluster identifies and uses the most appropriate capacity reservation from the group\. The resource group must have at least one ODCR for each instance type listed for the compute resource\. For more information, see [Launch instances with ODCR \(On\-Demand Capacity Reservations\)](launch-instances-odcr-v3.md)\.  
+ If `PlacementGroup` is enabled in [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking) or [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking), AWS ParallelCluster selects a resource group that targets the instance type and `PlacementGroup` for a compute resource if it exists\.

  The `PlacementGroup` must target one of the instances types defined in [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources)\.
+ If `PlacementGroup` isn't enabled in [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking) or [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources) / [`Networking`](#yaml-Scheduling-SlurmQueues-ComputeResources-Networking), AWS ParallelCluster selects a resource group that targets only the instance type of a compute resource, if it exists\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`CapacityReservationTarget` is added with AWS ParallelCluster version 3\.3\.0\.

`Networking`  

```
Networking:   
  PlacementGroup:
    Enabled: boolean
    Name: string
```
[Update policy: All compute nodes must be stopped for a managed placement group deletion. The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-remove-placement-group-v3)    
`PlacementGroup` \(**Optional**\)  
Specifies the placement group settings for the compute resource\.    
`Enabled` \(**Optional**, `Boolean`\)  
Indicates whether a placement group is used for the compute resource\.  
+ If set to `true`, without a `Name` defined, that compute resource is assigned its own managed placement group, regardless of the [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#Scheduling-v3-SlurmQueues-Networking) / [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) setting\.
+ If set to `true`, with a `Name` defined, that compute resource is assigned the named placement group, regardless of `SlurmQueues` / `Networking` / `PlacementGroup` settings\.
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Name` \(**Optional**, `String`\)  
The placement group name for an existing cluster placement group that's used for the compute resource\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)
+ If both `PlacementGroup` / `Enabled` and `Name` aren't set, their respective values default to the [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`Networking`](#Scheduling-v3-SlurmQueues-Networking) / [`PlacementGroup`](#yaml-Scheduling-SlurmQueues-Networking-PlacementGroup) settings\.
+ `ComputeResources` / `Networking` / `PlacementGroup` is added with AWS ParallelCluster version 3\.3\.0\.

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
      Throughput: integer
     EphemeralVolume:
      MountDir: string
```

[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`LocalStorage` \(**Optional**\)  
Specifies the properties of `LocalStorage` of the nodes in the Slurm queue\.  

```
LocalStorage:
  RootVolume:
    Size: integer
    Encrypted: boolean
    VolumeType: string
    Iops: integer
    Throughput: integer
  EphemeralVolume:
    MountDir: string
```
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)    
`RootVolume` \(**Optional**\)  
Specifies the details of the root volume of the nodes in the Slurm queue\.  

```
RootVolume:
  Size: integer
  Encrypted: boolean
  VolumeType: string
  Iops: integer
  Throughput: integer
```
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)    
`Size` \(**Optional**, `Integer`\)  
Specifies the root volume size in gibibytes \(GiB\) for the nodes in the Slurm queue\. The default size comes from the AMI\. Using a different size requires that the AMI supports `growroot`\.   
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Encrypted` \(**Optional**, `Boolean`\)  
If `true`, the root volume of the nodes in the Slurm queue are encrypted\. The default value is `false`\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`VolumeType` \(**Optional**, `String`\)  
Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) of the nodes in the Slurm queue\. Supported values are `gp2`, `gp3`, `io1`, `io2`, `sc1`, `st1`, and `standard`\. The default value is `gp3`\.  
For more information, see [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Iops` \(**Optional**, `Boolean`\)  
Defines the number of IOPS for `io1`, `io2`, and `gp3` type volumes\.  
The default value, supported values, and `volume_iops` to `volume_size` ratio varies by `VolumeType` and `Size`\.    
`VolumeType` = `io1`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 †  
Maximum `volume_iops` to `volume_size` ratio = 50 IOPS per GiB\. 5000 IOPS requires a `volume_size` of at least 100 GiB\.  
`VolumeType` = `io2`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 \(256000 for `io2` Block Express volumes\) †  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 10 GiB\.  
`VolumeType` = `gp3`  
Default `Iops` = 3000  
Supported values `Iops` = 3000–16000 †  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB for volumes with IOPS greater than 3000\.
† Maximum IOPS is guaranteed only on [Instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS\. Older `io1` volumes might not reach full performance unless you [modify the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\. `io2` Block Express volumes support `volume_iops` values up to 256000 on `R5b` instance types\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Throughput` \(**Optional**, `Integer`\)  
Defines the throughput for `gp3` volume types, in MiB/s\. This setting is valid only when `VolumeType` is `gp3`\. The default value is `125`\. Supported values: 125–1000 MiB/s  
The ratio of `Throughput` to `Iops` can be no more than 0\.25\. The maximum throughput of 1000 MiB/s requires that the `Iops` setting is at least 4000\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`EphemeralVolume` \(**Optional**, `Boolean`\)  
Specifies the settings for the ephemeral volume\. The ephemeral volume is created by combining all instance store volumes into a single logical volume formatted with the `ext4` file system\. The default is `/scratch`\. If the instance type does not have any instance store volumes then no ephemeral volume will be created\. For more information, see [Instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes) in the *Amazon EC2 User Guide for Linux Instances*\.  

```
EphemeralVolume:
  MountDir: string
```
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)    
`MountDir` \(**Optional**, `String`\)  
Specifies the mount directory for the ephemeral volume for each node in the Slurm queue\.   
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

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

[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

##### `CustomActions` Properties<a name="Scheduling-v3-SlurmQueues-CustomActions.properties"></a>

`OnNodeStart` \(**Optional**, `String`\)  
Specifies a script to run on the nodes in the Slurm queue before any node deployment bootstrap action is started\. For more information, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://` or `s3://`\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

`OnNodeConfigured` \(**Optional**, `String`\)  
Specifies a script to run on the nodes in the Slurm queue after all of the node bootstrap actions are complete\. For more information, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://`or `s3://`\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.  
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)
[Update policy: The compute fleet must be stopped or QueueUpdateStrategy must be set for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)

#### `Iam`<a name="Scheduling-v3-SlurmQueues-Iam"></a>

**\(Optional\)** Defines optional IAM settings for the Slurm queue\.

```
Iam:
  S3Access:
    - BucketName: string
      EnableWriteAccess: boolean
      KeyName: string
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
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

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
    KeyName: string
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
An IAM policy name and its ARN are different\. Names can't be used\. If the `InstanceProfile` or `InstanceRole` setting is specified, this setting is ignored\. We recommend that you use `AdditionalIamPolicies` because `AdditionalIamPolicies` are added to the permissions that AWS ParallelCluster requires, and the `InstanceRole` must include all permissions required\. The permissions required often change from release to release as features are added\.  
There is no default value\.  

```
AdditionalIamPolicies:
  - Policy: string
```
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`Policy` \(**Required**, `[String]`\)  
List of IAM policies\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `SlurmSettings`<a name="Scheduling-v3-SlurmSettings"></a>

**\(Optional\)** Defines the settings for Slurm that apply to the entire cluster\.

```
SlurmSettings:
  ScaledownIdletime: integer
  QueueUpdateStrategy: string
  EnableMemoryBasedScheduling: boolean
  Database:
    Uri: string
    UserName: string
    PasswordSecretArn: string
  Dns:
    DisableManagedDns: boolean
    HostedZoneId: string
    UseEc2Hostnames: boolean
```

### `SlurmSettings` Properties<a name="Scheduling-v3-SlurmSettings.properties"></a>

`ScaledownIdletime` \(**Optional**, `Integer`\)  
Defines the amount of time in minutes without a job, after which the Slurm node terminates\.  
The default value is `10`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`QueueUpdateStrategy` \(**Optional**, `String`\)  
Specifies the replacement strategy for the [`SlurmQueues`](#Scheduling-v3-SlurmQueues) section parameters that have the following update policy:  
[Update policy: The compute fleet must be stopped or `QueueUpdateStrategy` must be set for this setting to be changed for an update\.](using-pcluster-update-cluster-v3.md#update-policy-queue-update-strategy-v3)  
The `QueueUpdateStrategy` value is used only when a cluster update process starts\.  
Valid values: `COMPUTE_FLEET_STOP` \| `DRAIN` \| `TERMINATE`  
Default value: `COMPUTE_FLEET_STOP`    
`DRAIN`  
Nodes in queues with changed parameter values are set to `DRAINING`\. Nodes in this state don't accept new jobs and running jobs continue to completion\.  
Once a node becomes `idle` \(`DRAINED`\), if it's a static node, its replaced, and if it's a dynamic node, its terminated\. Other nodes in other queues without changed parameter values aren't impacted\.  
The time this strategy needs to replace all of the queue nodes with changed parameter values depends on the running workload\.  
`COMPUTE_FLEET_STOP`  
The default value of the `QueueUpdateStrategy` parameter\. With this setting, updating parameters under the [`SlurmQueues`](#Scheduling-v3-SlurmQueues) section requires you to [stop the compute fleet](pcluster.update-compute-fleet-v3.md) before performing a cluster update:  

```
$ pcluster update-compute-fleet --status STOP_REQUESTED
```  
`TERMINATE`  
In queues with changed parameter values, running jobs are terminated and the nodes are powered down immediately\.  
Static nodes are replaced and dynamic nodes are terminated\.  
Other nodes in other queues without changed parameter values aren't impacted\.
[Update policy: This setting is not analyzed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-ignored-v3)  
`QueueUpdateStrategy` is supported starting with AWS ParallelCluster version 3\.2\.0\.

`EnableMemoryBasedScheduling` \(**Optional**, `Boolean`\)  
If `true`, memory\-based scheduling is enabled in Slurm\. For more information, see [`SlurmQueues`](#Scheduling-v3-SlurmQueues) / [`ComputeResources`](#Scheduling-v3-SlurmQueues-ComputeResources) / [`SchedulableMemory`](#yaml-Scheduling-SlurmQueues-ComputeResources-SchedulableMemory)\.  
The default value is `false`\.  
Enabling memory\-based scheduling impacts the way the Slurm scheduler handles jobs and node allocation\.  
For more information, see [Slurm memory\-based scheduling](slurm-mem-based-scheduling-v3.md)\.
`EnableMemoryBasedScheduling` is supported starting with AWS ParallelCluster version 3\.2\.0\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

### `Database`<a name="Scheduling-v3-SlurmSettings-Database"></a>

**\(Optional\)** Defines the settings to enable Slurm Accounting on the cluster\. For more information, see [Slurm accounting with AWS ParallelCluster](slurm-accounting-v3.md)\.

```
Database:
   Uri: string
   UserName: string
   PasswordSecretArn: string
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

#### `Database` Properties<a name="Scheduling-v3-SlurmSettings-Database.properties"></a>

`Uri` \(**Required**, `String`\)  
The address to the database server that's used as the backend for Slurm accounting\. This URI must be formatted as `host:port` and must not contain a scheme, such as `mysql://`\. The host can either be an IP address or a DNS name that's resolvable by the head node\. If a port isn't provided, AWS ParallelCluster uses the mysql default port 3306\.  
AWS ParallelCluster bootstraps the Slurm accounting database to the cluster and must be able to access the database\.  
The database must be reachable before:  
+ A cluster is created\.
+ Slurm accounting is enabled with a cluster update\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`UserName` \(**Required**, `String`\)  
The identity used by Slurm to connect to the database, write accounting logs, and perform queries\. The user must have both read and write permissions on the database\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`PasswordSecretArn` \(**Required**, `String`\)  
The Amazon Resource Name \(ARN\) of the AWS Secrets Manager secret that contains the `UserName` plaintext password\. This password is used together with `UserName` and Slurm accounting to authenticate on the database server\.  
If the user has the permission to [DescribeSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html), `PasswordSecretArn` is validated\. `PasswordSecretArn` is valid if the specified secret exists\. If the user IAM policy doesn't include `DescribeSecret`, `PasswordSecretArn` isn't validated and a warning message is displayed\. For more information, see [Base user policy required to invoke AWS ParallelCluster features](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-base-user-policy)\.  
Updating `PasswordSecretArn` requires the compute fleet to be stopped\. When the secret value changes, with no change to the secret ARN, the cluster isn't automatically updated with the new database password\. To update the cluster for the new secret value, you must run the following command from within the head node after the compute fleet is stopped\.  

```
$ sudo /opt/parallelcluster/scripts/slurm/update_slurm_database_password.sh
```
We recommend that you only change the database password when the compute fleet is stopped to avoid loss of accounting data\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

**Note**  
`Database` is added starting with release 3\.3\.0\.

### `Dns`<a name="Scheduling-v3-SlurmSettings-Dns"></a>

**\(Optional\)** Defines the settings for Slurm that apply to the entire cluster\.

```
Dns:
  DisableManagedDns: boolean
  HostedZoneId: string
  UseEc2Hostnames: boolean
```

#### `Dns` Properties<a name="Scheduling-v3-SlurmSettings-Dns.properties"></a>

`DisableManagedDns` \(**Optional**, `Boolean`\)  
If `true`, the DNS entries for the cluster shouldn't be created\. By default, AWS ParallelCluster creates a Route 53 hosted zone\. The default value is `false`\. If `DisableManagedDns` is set to `true`, the hosted zone isn't created by AWS ParallelCluster\.  
A name resolution system is required for the cluster to operate properly\. If `DisableManagedDns` is set to `true`, an additional name resolution system must also be provided\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`HostedZoneId` \(**Optional**, `String`\)  
Defines a custom Route 53 hosted zone id to use for DNS name resolution for the cluster\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`UseEc2Hostnames` \(**Optional**, `Boolean`\)  
Specifies the use of default EC2 hostnames if set to `true`\. The default is `false`\.  
**This note isn't relevant starting with AWS ParallelCluster version 3\.3\.0\.**  
For AWS ParallelCluster supported versions prior to 3\.3\.0:  
When `UseEc2Hostnames` is set to `true`, the Slurm configuration file is set with the AWS ParallelCluster `prolog` and `epilog` scripts:  
+ `prolog` runs to add nodes info to `/etc/hosts` on compute nodes when each job is allocated\.
+ `epilog` runs to clean contents written by `prolog`\.
To add custom `prolog` or `epilog` scripts, add them to the `/opt/slurm/etc/pcluster/prolog.d/` or `/opt/slurm/etc/pcluster/epilog.d/` folders respectively\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)