# `HeadNode` section<a name="HeadNode-v3"></a>

**\(Required\)** Specifies the configuration for the head node\.

```
HeadNode:
  InstanceType: string
  Networking:
    SubnetId: string
    ElasticIp: string/boolean
    SecurityGroups:
      - string
    AdditionalSecurityGroups:
      - string
    Proxy:
      HttpProxyAddress: string
  DisableSimultaneousMultithreading: boolean
  Ssh:
    KeyName: string
    AllowedIps: string
  LocalStorage:
    RootVolume:
      Size: integer
      Encrypted: boolean
      VolumeType: string
      Iops: integer
      Throughput: integer
      DeleteOnTermination: boolean
    EphemeralVolume:
      MountDir: string
  Dcv:
    Enabled: boolean
    Port: integer
    AllowedIps: string
  CustomActions:
    OnNodeStart:
      Script: string
      Args:
        - string
    OnNodeConfigured:
      Script: string
      Args:
        - string
    OnNodeUpdated:
      Script: string
      Args:
        - string
  Iam:
    InstanceRole: string
    InstanceProfile: string
    S3Access:
      - BucketName: string
        EnableWriteAccess: boolean
        KeyName: string
    AdditionalIamPolicies:
      - Policy: string
  Imds:
    Secured: boolean
  Image:
    CustomAmi: string
```

## `HeadNode` properties<a name="HeadNode-v3.properties"></a>

`InstanceType` \(**Required**, `String`\)  
Specifies the instance type for the head node\.  
Specifies the Amazon EC2 instance type that's used for the head node\. The architecture of the instance type must be the same as the architecture used for the AWS Batch [`InstanceType`](Scheduling-v3.md#yaml-Scheduling-AwsBatchQueues-ComputeResources-InstanceTypes) or Slurm [`InstanceType`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-InstanceType) setting\.  
AWS ParallelCluster doesn't support the following instance types for the `HeadNode` setting\.  
+ hpc6id
If you define a p4d instance type or another instance type that has multiple network interfaces or a network interface card, you must set [`ElasticIp`](#yaml-HeadNode-Networking-ElasticIp) to `true` to provide public access\. AWS public IPs can only be assigned to instances launched with a single network interface\. For this case, we recommend that you use a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) to provide public access to the cluster compute nodes\. For more information, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`DisableSimultaneousMultithreading` \(**Optional**, `Boolean`\)  
If `true`, disables hyperthreading on the head node\. The default value is `false`\.  
Not all instance types can disable hyperthreading\. For a list of instance types that support disabling hyperthreading, see [CPU cores and threads for each CPU core per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-optimize-cpu.html#cpu-options-supported-instances-values) in the *Amazon EC2 User Guide for Linux Instances*\.   
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `Networking`<a name="HeadNode-v3-Networking"></a>

**\(Required\)** Defines the networking configuration for the head node\.

```
Networking:
  SubnetId: string
  ElasticIp: string/boolean
  SecurityGroups:
    - string
  AdditionalSecurityGroups:
    - string
  Proxy:
    HttpProxyAddress: string
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

### `Networking` properties<a name="HeadNode-v3-Networking.properties"></a>

`SubnetId` \(**Required**, `String`\)  
Specifies the ID of an existing subnet in which to provision the head node\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`ElasticIp` \(**Optional**, `String`\)  
Creates or assigns an Elastic IP address to the head node\. Supported values are `true`, `false`, or the ID of an existing Elastic IP address\. The default is `false`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`SecurityGroups` \(**Optional**, `[String]`\)  
List of Amazon VPC security group ids to use for the head node\. These replace the security groups that AWS ParallelCluster creates if this property is not included\.  
Verify that the security groups are configured correctly for your [SharedStorage](SharedStorage-v3.md) systems\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`AdditionalSecurityGroups` \(**Optional**, `[String]`\)  
List of additional Amazon VPC security group ids to use for the head node\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`Proxy` \(**Optional**\)  
Specifies the proxy settings for the head node\.  

```
Proxy:
  HttpProxyAddress: string
```  
`HttpProxyAddress` \(**Optional**, `String`\)  
Defines an HTTP or HTTPS proxy server, typically `https://x.x.x.x:8080`\.  
There is no default value\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `Ssh`<a name="HeadNode-v3-Ssh"></a>

**\(Optional\)** Defines the configuration for SSH access to the head node\.

```
Ssh:
  KeyName: string
  AllowedIps: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

### `Ssh` properties<a name="HeadNode-v3-Ssh.properties"></a>

`KeyName` \(**Optional**, `String`\)  
Names an existing Amazon EC2 key pair to enable SSH access to the head node\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`AllowedIps` \(**Optional**, `String`\)  
Specifies the CIDR\-formatted IP range for SSH connections to the head node\. The default is `0.0.0.0/0`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `LocalStorage`<a name="HeadNode-v3-LocalStorage"></a>

**\(Optional\)** Defines the local storage configuration for the head node\.

```
LocalStorage:
  RootVolume:
    Size: integer
    Encrypted: boolean
    VolumeType: string
    Iops: integer
    Throughput: integer
    DeleteOnTermination: boolean
  EphemeralVolume:
    MountDir: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

### `LocalStorage` properties<a name="HeadNode-v3-LocalStorage.properties"></a>

`RootVolume` \(**Required**\)  
Specifies the root volume storage for the head node\.  

```
RootVolume:
  Size: integer
  Encrypted: boolean
  VolumeType: string
  Iops: integer
  Throughput: integer
  DeleteOnTermination: boolean
```
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`Size` \(**Optional**, `Integer`\)  
Specifies the head node root volume size in gibibytes \(GiB\)\. The default size comes from the AMI\. Using a different size requires that the AMI supports `growroot`\.   
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
`Encrypted` \(**Optional**, `Boolean`\)  
Specifies if the root volume is encrypted\. The default value is `true`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
`VolumeType` \(**Optional**, `String`\)  
Specifies the [Amazon EBS volume type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)\. Supported values are `gp2`, `gp3`, `io1`, `io2`, `sc1`, `st1`, and `standard`\. The default value is `gp3`\.  
For more information, see [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
`Iops` \(**Optional**, `Integer`\)  
Defines the number of IOPS for `io1`, `io2`, and `gp3` type volumes\.  
The default value, supported values, and `volume_iops` to `volume_size` ratio varies by `VolumeType` and `Size`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)    
`VolumeType` = `io1`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 †  
Maximum `Iops` to `Size` ratio = 50 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 100 GiB\.  
`VolumeType` = `io2`  
Default `Iops` = 100  
Supported values `Iops` = 100–64000 \(256000 for `io2` Block Express volumes\) †  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 10 GiB\.  
`VolumeType` = `gp3`  
Default `Iops` = 3000  
Supported values `Iops` = 3000–16000  
Maximum `Iops` to `Size` ratio = 500 IOPS per GiB\. 5000 IOPS requires a `Size` of at least 10 GiB\.
† Maximum IOPS is guaranteed only on [Instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS\. Older `io1` volumes might not reach full performance unless you [modify the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\. `io2` Block Express volumes support `Iops` values up to 256000 on `R5b` instance types\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`Throughput` \(**Optional**, `Integer`\)  
Defines the throughput for `gp3` volume types, in MiB/s\. This setting is valid only when `VolumeType` is `gp3`\. The default value is `125`\. Supported values: 125–1000 MiB/s  
The ratio of `Throughput` to `Iops` can be no more than 0\.25\. The maximum throughput of 1000 MiB/s requires that the `Iops` setting is at least 4000\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
`DeleteOnTermination` \(**Optional**, `Boolean`\)  
Specifies whether the root volume should be deleted when the head node is terminated\. The default value is `true`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`EphemeralVolume` \(**Optional**\)  
Specifies details for any instance store volume\. For more information, see [Instance store volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes) in the *Amazon EC2 User Guide for Linux Instances*\.  

```
EphemeralVolume:
  MountDir: string
```
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)    
`MountDir` \(**Optional**, `String`\)  
Specifies the mount directory for the instance store volume\. The default is `/scratch`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `Dcv`<a name="HeadNode-v3-Dcv"></a>

**\(Optional\)** Defines configuration settings for the NICE DCV server running on the head node\.

For more information, see [Connect to the head node through NICE DCV](dcv-v3.md)\.

```
Dcv:
  Enabled: boolean
  Port: integer
  AllowedIps: string
```

**Important**  
By default, the NICE DCV port setup by AWS ParallelCluster is open to all IPv4 addresses\. However, you can connect to a NICE DCV port only if you have the URL for the NICE DCV session and connect to the NICE DCV session within 30 seconds of when the URL is returned from `pcluster dcv-connect`\. Use the `AllowedIps` setting to further restrict access to the NICE DCV port with a CIDR\-formatted IP range, and use the `Port` setting to set a nonstandard port\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

### `Dcv` properties<a name="HeadNode-v3-Dcv.properties"></a>

`Enabled` \(**Required**, `Boolean`\)  
Specifies whether NICE DCV is enabled on the head node\. The default value is `false`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
NICE DCV automatically generates a self\-signed certificate that's used to secure traffic between the NICE DCV client and NICE DCV server running on the head node\. To configure your own certificate, see [NICE DCV HTTPS certificate](dcv.md#dcv-certificate)\.

`Port` \(**Optional**, `Integer`\)  
Specifies the port for NICE DCV\. The default value is `8443`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`AllowedIps` \(**Optional, Recommended**, `String`\)  
Specifies the CIDR\-formatted IP range for connections to NICE DCV\. This setting is used only when AWS ParallelCluster creates the security group\. The default value is `0.0.0.0/0`, which allows access from any internet address\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `CustomActions`<a name="HeadNode-v3-CustomActions"></a>

**\(Optional\)** Specifies custom scripts to run on the head node\.

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
   OnNodeUpdated:
   Script: string
   Args:
     - string
```

### `CustomActions` properties<a name="HeadNode-v3-CustomActions.properties"></a>

`OnNodeStart` \(**Optional**, `String`\)  
Specifies a script to run on the head node before any node deployment bootstrap action is started\. For more information, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://` or `s3://`\.  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`OnNodeConfigured` \(**Optional**, `String`\)  
Specifies a script to run on the head node after the node bootstrap actions are complete\. For more information, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://` or `s3://`\.  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`OnNodeUpdated` \(**Optional**, `String`\)  
Specifies a script to run on the head node after node update actions are complete\. For more information, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.    
`Script` \(**Required**, `String`\)  
Specifies the file to use\. The file path can start with `https://` or `s3://`\.  
`Args` \(**Optional**, `[String]`\)  
List of arguments to pass to the script\.
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`OnNodeUpdated` is added starting with AWS ParallelCluster 3\.4\.0\.

## `Iam`<a name="HeadNode-v3-Iam"></a>

**\(Optional\)** Specifies either an instance role or an instance profile to use on the head node to override the default instance role or instance profile for the cluster\.

```
Iam:
  InstanceRole: string
  InstanceProfile: string
  S3Access:
    - BucketName: string
      EnableWriteAccess: boolean
      KeyName: string
  AdditionalIamPolicies:
    - Policy: string
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

### `Iam` properties<a name="HeadNode-v3-Iam.properties"></a>

`InstanceProfile` \(**Optional**, `String`\)  
Specifies an instance profile to override the default head node instance profile\. You can't specify both `InstanceProfile` and `InstanceRole`\. The format is `arn:Partition:iam::Account:instance-profile/InstanceProfileName`\.  
If this is specified, the `S3Access` and `AdditionalIamPolicies` settings can't be specified\.  
We recommend that you specify one or both of the `S3Access` and `AdditionalIamPolicies` settings because features added to AWS ParallelCluster often require new permissions\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`InstanceRole` \(**Optional**, `String`\)  
Specifies an instance role to override the default head node instance role\. You can't specify both `InstanceProfile` and `InstanceRole`\. The format is `arn:Partition:iam::Account:role/RoleName`\.  
If this is specified, the `S3Access` and `AdditionalIamPolicies` settings can't be specified\.  
We recommend that you specify one or both of the `S3Access` and `AdditionalIamPolicies` settings because features added to AWS ParallelCluster often require new permissions\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

### `S3Access`<a name="HeadNode-v3-Iam-S3Access.properties"></a>

`S3Access` \(**Optional**\)  
Specifies a bucket\. This is used to generate policies to grant the specified access to the bucket\.  
If this is specified, the `InstanceProfile` and `InstanceRole` settings can't be specified\.  
We recommend that you specify one or both of the `S3Access` and `AdditionalIamPolicies` settings because features added to AWS ParallelCluster often require new permissions\.  

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
The key for the bucket\. The default value is "`*`"\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`EnableWriteAccess` \(**Optional**, `Boolean`\)  
Indicates whether write access is enabled for the bucket\. The default value is `false`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

### `AdditionalIamPolicies`<a name="HeadNode-v3-Iam-AdditionalIamPolicies.properties"></a>

`AdditionalIamPolicies` \(**Optional**\)  
Specifies a list of Amazon Resource Names \(ARNs\) of IAM policies for Amazon EC2\. This list is attached to the root role used for the head node in addition to the permissions required by AWS ParallelCluster\.  
An IAM policy name and its ARN are different\. Names can't be used\.  
If this is specified, the `InstanceProfile` and `InstanceRole` settings can't be specified\.  
We recommend that you use `AdditionalIamPolicies` because `AdditionalIamPolicies` are added to the permissions that AWS ParallelCluster requires, and the `InstanceRole` must include all permissions required\. The permissions required often change from release to release as features are added\.  
There is no default value\.  

```
AdditionalIamPolicies:
  - Policy: string
```
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`Policy` \(**Optional**, `[String]`\)  
List of IAM policies\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `Imds`<a name="HeadNode-v3-Imds"></a>

**\(Optional\)** Specifies the properties for instance metadata service \(IMDS\)\. For more information, see [How instance metadata service version 2 works](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html#instance-metadata-v2-how-it-works) in the *Amazon EC2 User Guide for Linux Instances*\.

```
Imds:
    Secured: boolean
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

### `Imds` properties<a name="HeadNode-v3-Imds.properties"></a>

`Secured` \(**Optional**, `Boolean`\)  
If `true`, restricts access to the head node's IMDS \(and the instance profile credentials\) to a subset of superusers\.  
If `false`, every user in the head node has access to the head node's IMDS\.  

The following users are permitted access to the head node's IMDS:
+ root user
+ cluster administrative user \(`pc-cluster-admin` by default\)
+ operating system specific default user \(`ec2-user` on Amazon Linux 2, `ubuntu` on Ubuntu 18\.04, `centos` on CentOS 7\)
The default is `true`\.  
The `default` users are responsible for ensuring a cluster has the permissions it needs to interact with AWS resources\. If you disable `default` user IMDS access, AWS ParallelCluster can't manage the compute nodes and stops working\. Don't disable `default` user IMDS access\.  
When a user is granted access to the head node's IMDS, they can use the permissions included in the [head node's instance profile](iam-roles-in-parallelcluster-v3.md)\. For example, they can use these permissions to launch EC2 instances or to read the password for an AD domain that the cluster is configured to use for authentication\.  
To restrict IMDS access, AWS ParallelCluster manages a chain of `iptables`\.  
Cluster users with `sudo` access can selectively enable or disable access to the head node's IMDS for other individual users, including `default` users, by running the command:  

```
$ sudo /opt/parallelcluster/scripts/imds/imds-access.sh --allow <USERNAME>
```
You can disable user IMDS access with the `--deny` option for this command\.  
If you unknowingly disable `default` user IMDS access, you can restore the permission by using the `--allow` option\.  
Any customization of `iptables` or `ip6tables` rules can interfere with the mechanism used to restrict IMDS access on the head node\.
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `Image`<a name="HeadNode-v3-Image"></a>

**\(Optional\)** Defines a custom image for the head node\.

```
Image:
     CustomAmi: string
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

### `Image` properties<a name="HeadNode-v3-Image.properties"></a>

`CustomAmi` \(**Optional**, `String`\)  
Specifies the ID of a custom AMI to use for the head node instead of the default AMI\. For more information, see [AWS ParallelCluster AMI customization](custom-ami-v3.md)\.  
If the custom AMI requires additional permissions for its launch, these permissions must be added to both the user and head node policies\.  
For example, if a custom AMI has an encrypted snapshot associated with it, the following additional policies are required in both the user and head node policies:  

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
To troubleshoot custom AMI validation warnings, see [Troubleshooting custom AMI issues](troubleshooting-v3-custom-amis.md)\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)