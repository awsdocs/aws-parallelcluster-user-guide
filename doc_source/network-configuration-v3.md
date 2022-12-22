# Network configurations<a name="network-configuration-v3"></a>

AWS ParallelCluster uses Amazon Virtual Private Cloud \(VPC\) for networking\. VPC provides a flexible and configurable networking platform where you can deploy clusters\.

The VPC must have `DNS Resolution = yes`, `DNS Hostnames = yes` and DHCP options with the correct domain\-name for the Region\. The default DHCP Option Set already specifies the required *AmazonProvidedDNS*\. If specifying more than one domain name server, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

AWS ParallelCluster supports the following high\-level configurations:
+  One subnet for both head and compute nodes\. 
+  Two subnets, with the head node in one public subnet, and compute nodes in a private subnet\. The subnets can be either new or existing ones\. 

All of these configurations can operate with or without public IP addressing\. AWS ParallelCluster can also be deployed to use an HTTP proxy for all AWS requests\. The combinations of these configurations result in many deployment scenarios\. For example, you can configure a single public subnet with all access over the internet\. Or, you can configure a fully private network using AWS Direct Connect and HTTP proxy for all traffic\.

Starting from AWS ParallelCluster 3\.0\.0 it is possible to configure different `SecurityGroups`, `AdditionalSecurityGroups` and `PlacementGroup` settings for each queue\. For more information, see [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) and [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) and [`AwsBatchQueues`](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues-Networking)\.

For illustrations of some networking scenarios, see the following architecture diagrams\.

**Topics**
+ [AWS ParallelCluster in a single public subnet](#network-configuration-v3-single-subnet)
+ [AWS ParallelCluster using two subnets](#network-configuration-v3-two-subnets)
+ [AWS ParallelCluster in a single private subnet connected using AWS Direct Connect](#network-configuration-v3-single-subnet-direct-connect)
+ [AWS ParallelCluster with AWS Batch scheduler](#network-configuration-v3-batch)
+ [AWS ParallelCluster in a single subnet with no internet access](#aws-parallelcluster-in-a-single-public-subnet-no-internet-v3)

## AWS ParallelCluster in a single public subnet<a name="network-configuration-v3-single-subnet"></a>

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/single-public-subnet.png) 

 The configuration for this architecture requires the following settings:

```
# Note that all values are only provided as examples
HeadNode:
  ...
  Networking:
    SubnetId: subnet-12345678 # subnet with internet gateway
    #ElasticIp: true | false | eip-12345678
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - ...
      Networking:
        SubnetIds:
          - subnet-12345678 # subnet with internet gateway
        #AssignPublicIp: true
```

In this configuration, all instances of the cluster must be assigned a public IP in order to get internet access\. To achieve this, do the following:
+ Make sure the head node is assigned a public IP address by either turning on the "Enable auto\-assign public IPv4 address" setting for the subnet used in [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`SubnetId`](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId) or by assigning an Elastic IP in [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`ElasticIp`](HeadNode-v3.md#yaml-HeadNode-Networking-ElasticIp)\.
+ Make sure the compute nodes are assigned a public IP address by either turning on the "Enable auto\-assign public IPv4 address" setting for the subnet used in [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) / [`SubnetIds`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-SubnetIds) or by setting [`AssignPublicIp`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-AssignPublicIp): true in [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking)\.
+ If you define a p4d instance type or another instance type that has multiple network interfaces or a network interface card to the head node, you must set [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`ElasticIp`](HeadNode-v3.md#yaml-HeadNode-Networking-ElasticIp) to `true` to provide public access\. AWS public IPs can only be assigned to instances launched with a single network interface\. For this case, we recommend that you use a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) to provide public access to the cluster compute nodes\. For more information on IP addresses, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.
+ You can't define a p4d instance type or another instance type that has multiple network interfaces or a network interface card to compute nodes because AWS public IPs can only be assigned to instances launched with a single network interface\. For more information on IP addresses, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.

For more information, see [ Enabling internet access](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#vpc-igw-internet-access) in *Amazon VPC User Guide*\.

## AWS ParallelCluster using two subnets<a name="network-configuration-v3-two-subnets"></a>

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/two-subnets.png) 

 The configuration to use an existing private subnet for compute instances requires the following settings:

```
# Note that all values are only provided as examples
HeadNode:
  ...
  Networking:
    SubnetId: subnet-12345678 # subnet with internet gateway
    #ElasticIp: true | false | eip-12345678
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - ...
      Networking:
        SubnetIds:
          - subnet-23456789 # subnet with NAT gateway
        #AssignPublicIp: false
```

In this configuration, only the head node of the cluster is required to have a public IP assigned\. You can achieve this by either turning on the "Enable auto\-assign public IPv4 address" setting for the subnet used in [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`SubnetId`](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId) or by assigning an Elastic IP in [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`ElasticIp`](HeadNode-v3.md#yaml-HeadNode-Networking-ElasticIp)\.

If you define a p4d instance type or another instance type that has multiple network interfaces or a network interface card to the head node, you must set [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`ElasticIp`](HeadNode-v3.md#yaml-HeadNode-Networking-ElasticIp) to `true` to provide public access\. AWS public IPs can only be assigned to instances launched with a single network interface\. For more information on IP addresses, see [Assign a public IPv4 address during instance launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#public-ip-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.

This configuration requires a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an internal proxy in the subnet used for the queues, to give internet access to the compute instances\.

## AWS ParallelCluster in a single private subnet connected using AWS Direct Connect<a name="network-configuration-v3-single-subnet-direct-connect"></a>

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/single-private-DirectConnect.png) 

 The configuration for this architecture requires the following settings:

```
# Note that all values are only provided as examples
HeadNode:
  ...
  Networking:
    SubnetId: subnet-34567890 # subnet with proxy
    Proxy:
      HttpProxyAddress: http://proxy-address:port
  Ssh:
    KeyName: ec2-key-name
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - ...
      Networking:
        SubnetIds:
          - subnet-34567890 # subnet with proxy
        AssignPublicIp: false
        Proxy:
          HttpProxyAddress: http://proxy-address:port
```

When [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Networking) / [`AssignPublicIp`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-AssignPublicIp) is set to `false`, the subnets must be correctly set up to use the Proxy for all traffic\. Web access is required for both head and compute nodes\. 

## AWS ParallelCluster with AWS Batch scheduler<a name="network-configuration-v3-batch"></a>

When you use `awsbatch` as the scheduler type, AWS ParallelCluster creates an AWS Batch managed compute environment\. The AWS Batch environment manages Amazon Elastic Container Service \(Amazon ECS\) container instances\. These instances are launched in the subnet configured in the [`AwsBatchQueues`](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues-Networking) / [`SubnetIds`](Scheduling-v3.md#yaml-Scheduling-AwsBatchQueues-Networking-SubnetIds) parameter\. For AWS Batch to function correctly, Amazon ECS container instances need external network access to communicate with the Amazon ECS service endpoint\. This translates into the following scenarios: 
+ The Subnet ID specified for the queue uses a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) to access the internet\. We recommended this approach\.
+ Instances launched in the queue subnet have public IP addresses and can reach the internet through an Internet Gateway\. 

Additionally, if you're interested in multi\-node parallel jobs \(from the [AWS Batch docs](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html#mnp-ce)\):

AWS Batch multi\-node parallel jobs use the Amazon ECS `awsvpc` network mode\. This gives your multi\-node parallel job containers the same networking properties as Amazon EC2 instances\. Each multi\-node parallel job container gets its own elastic network interface, a primary private IP address, and an internal DNS hostname\. The network interface is created in the same Amazon VPC subnet as its host compute resource\. Any security groups that are applied to your compute resources are also applied to it\.

When using Amazon ECS Task Networking, the `awsvpc` network mode doesn't provide elastic network interfaces with public IP addresses for tasks that use the Amazon EC2 launch type\. To access the internet, tasks that use the Amazon EC2 launch type must be launched in a private subnet that's configured to use a NAT gateway\.

You must configure a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in order to enable the cluster to run multi\-node parallel jobs\.

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/two-subnets-batch.png) 

All the previous configuration and considerations are valid for AWS Batch, too\. The following is an example of a AWS Batch networking configuration\.

```
# Note that all values are only provided as examples
HeadNode:
  ...
  Networking:
    SubnetId: subnet-12345678 # subnet with internet gateway, NAT gateway or proxy
    #ElasticIp: true | false | eip-12345678
    #Proxy:
      #HttpProxyAddress: http://proxy-address:port
  Ssh:
    KeyName: ec2-key-name
Scheduling:
  Scheduler: awsbatch
  AwsBatchQueues:
    - ...
      Networking:
        SubnetIds:
          - subnet-23456789 # subnet with internet gateway, NAT gateway or proxy
        #AssignPublicIp: true | false
```

In the [`Scheduling`](Scheduling-v3.md) / [`AwsBatchQueues`](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues) / [`Networking`](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues-Networking) section, the [`SubnetIds`](Scheduling-v3.md#yaml-Scheduling-AwsBatchQueues-Networking-SubnetIds) is a list type but, currently, only one subnet is supported\.

For more information, see the following topics:
+  [AWS Batch managed compute environments](https://docs.aws.amazon.com/batch/latest/userguide/compute_environments.html#managed_compute_environments) 
+  [AWS Batch multi\-node parallel jobs](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html) 
+  [Amazon ECS task networking with the awsvpc network mode](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) 

## AWS ParallelCluster in a single subnet with no internet access<a name="aws-parallelcluster-in-a-single-public-subnet-no-internet-v3"></a>

![\[AWS ParallelCluster using one subnet and no internet\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/networking_single_subnet_no_internet.png)

A subnet without internet access doesn't allow inbound or outbound connections to the internet\. This AWS ParallelCluster configuration can help security\-concerned customers further enhance the security of their AWS ParallelCluster resources\. AWS ParallelCluster nodes are built from AWS ParallelCluster AMIs that include all of the software that's required to run a cluster with no internet access\. This way, AWS ParallelCluster can create and manage clusters with nodes that don't have internet access\.

In this section, you learn about how to configure the cluster\. You also learn about limitations in running clusters without internet access\.

**Configuring VPC endpoints**

To ensure the proper functioning of the cluster, the cluster nodes must be able to interact with a number of AWS Services\.

Create and configure the following [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html) so that cluster nodes can interact with the AWS Services, without internet access:

------
#### [ Commercial and AWS GovCloud \(US\) partitions ]


| Service | Service name | Type | 
| --- | --- | --- | 
|  Amazon CloudWatch  |  com\.amazonaws\.*region\-id*\.logs  |  Interface  | 
|  AWS CloudFormation  |  com\.amazonaws\.*region\-id*\.cloudformation  |  Interface  | 
|  Amazon EC2  |  com\.amazonaws\.*region\-id*\.ec2  |  Interface  | 
|  Amazon S3  |  com\.amazonaws\.*region\-id*\.s3  |  Gateway  | 
|  Amazon DynamoDB  |  com\.amazonaws\.*region\-id*\.dynamodb  |  Gateway  | 
|  AWS Secrets Manager\*\*  |  com\.amazonaws\.*region\-id*\.secretsmanager  |  Interface  | 

------
#### [ China partition ]


| Service | Service name | Type | 
| --- | --- | --- | 
|  Amazon CloudWatch  |  com\.amazonaws\.*region\-id*\.logs  |  Interface  | 
|  AWS CloudFormation  |  cn\.com\.amazonaws\.*region\-id*\.cloudformation  |  Interface  | 
|  Amazon EC2  |  cn\.com\.amazonaws\.*region\-id*\.ec2  |  Interface  | 
|  Amazon S3  |  com\.amazonaws\.*region\-id*\.s3  |  Gateway  | 
|  Amazon DynamoDB  |  com\.amazonaws\.*region\-id*\.dynamodb  |  Gateway  | 
|  AWS Secrets Manager\*\*  |  com\.amazonaws\.*region\-id*\.secretsmanager  |  Interface  | 

------

\*\* This endpoint is only required when [`DirectoryService`](DirectoryService-v3.md#DirectoryService-v3.properties) is enabled, otherwise it is optional\.

All instances in the VPC must have proper security groups to communicate with the endpoints\. You can do this by adding security groups to [`AdditionalSecurityGroups`](HeadNode-v3.md#yaml-HeadNode-Networking-AdditionalSecurityGroups) under the [`HeadNode`](HeadNode-v3.md) and [`AdditionalSecurityGroups`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Networking-AdditionalSecurityGroups) under the [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) configurations\. For example, if the VPC endpoints are created without explicitly specifying a security group, the default security group that's associated with the endpoint\. By adding the default security group to `AdditionalSecurityGroups`, you enable the communication between the cluster and the endpoints\.

**Note**  
When you use IAM policies to restrict access to VPC endpoints, you must add the following to the Amazon S3 VPC endpoint:  

```
PolicyDocument:
  Version: 2012-10-17
  Statement:
    - Effect: Allow
      Principal: "*"
      Action:
        - "s3:PutObject"
      Resource:
        - !Sub "arn:${AWS::Partition}:s3:::cloudformation-waitcondition-${AWS::Region}/*"
```

**Disable Route 53 and use EC2 hostnames**

When creating a Slurm cluster, AWS ParallelCluster creates a private Route 53 hosted zone that is used to resolve the custom compute node hostnames, such as `{queue_name}-{st|dy}-{compute_resource}-{N}`\. Because Route 53 doesn't support VPC endpoints, this feature must be disabled\. Additionally, AWS ParallelCluster must be configured to use the default EC2 hostnames, such as `ip-1-2-3-4`\. Apply the following settings to your cluster configuration:

```
...
Scheduling:
  ...
  SlurmSettings:
    Dns:
      DisableManagedDns: true
      UseEc2Hostnames: true
```

**Warning**  
For clusters created with [`SlurmSettings`](Scheduling-v3.md#Scheduling-v3-SlurmSettings) / [`Dns`](Scheduling-v3.md#Scheduling-v3-SlurmSettings-Dns) / [`DisableManagedDns`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Dns-DisableManagedDns) and [`UseEc2Hostnames`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Dns-UseEc2Hostnames) set to `true`, the Slurm `NodeName` isn't resolved by the DNS\. Use the Slurm `NodeHostName` instead\.

**Note**  
**This note isn't relevant starting with AWS ParallelCluster version 3\.3\.0\.**  
For AWS ParallelCluster supported versions prior to 3\.3\.0:  
When `UseEc2Hostnames` is set to `true`, the Slurm configuration file is set with the AWS ParallelCluster `prolog` and `epilog` scripts:  
`prolog` runs to add nodes info to `/etc/hosts` on compute nodes when each job is allocated\.
`epilog` runs to clean contents written by `prolog`\.
To add custom `prolog` or `epilog` scripts, add them to the `/opt/slurm/etc/pcluster/prolog.d/` or `/opt/slurm/etc/pcluster/epilog.d/` folders respectively\.

**Cluster configuration**

Learn how to configure your cluster to run in a subnet with no connection to the internet\.

The configuration for this architecture requires the following settings:

```
# Note that all values are only provided as examples
...
HeadNode:
  ...
  Networking:
    SubnetId: subnet-1234567890abcdef0 # the VPC of the subnet needs to have VPC endpoints
    AdditionalSecurityGroups:
      - sg-abcdef01234567890 # optional, the security group that enables the communication between the cluster and the VPC endpoints
Scheduling:
  Scheduler: slurm # Cluster in a subnet without internet access is supported only when the scheduler is Slurm.
  SlurmSettings:
    Dns:
      DisableManagedDns: true
      UseEc2Hostnames: true
  SlurmQueues:
    - ...
      Networking:
        SubnetIds:
          - subnet-1234567890abcdef0 # the VPC of the subnet needs to have VPC endpoints attached
        AdditionalSecurityGroups:
          - sg-1abcdef01234567890 # optional, the security group that enables the communication between the cluster and the VPC endpoints
```
+ [`SubnetId(s)`](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId): The subnet without internet access\.

  To enable communication between AWS ParallelCluster and AWS Services, the VPC of the subnet must have the VPC endpoints attached\. Before you create your cluster, verify that [auto\-assign public IPv4 address is disabled](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip) in the subnet to ensure that the `pcluster` commands have access to the cluster\.
+ [`AdditionalSecurityGroups`](HeadNode-v3.md#yaml-HeadNode-Networking-AdditionalSecurityGroups): The security group that enables the communication between the cluster and the VPC endpoints\.

  Optional:
  + If the VPC endpoints are created without explicitly specifying a security group, the default security group of the VPC is associated\. Therefore, provide the default security group to `AdditionalSecurityGroups`\.
  + If custom security groups are used when creating the cluster and/or the VPC endpoints, `AdditionalSecurityGroups` is unnecessary as long as the custom security groups enable communication between the cluster and the VPC endpoints\.
+ [`Scheduler`](Scheduling-v3.md#yaml-Scheduling-Scheduler): The cluster scheduler\.

  `slurm` is the only valid value\. Only the Slurm scheduler supports a cluster in a subnet without internet access\.
+ [`SlurmSettings`](Scheduling-v3.md#Scheduling-v3-SlurmSettings): The Slurm settings\.

  See the preceding section *Disable Route53 and use EC2 hostnames*\.

**Limitations**
+ *Connecting to the head node over SSH or NICE DCV:* When connecting to a cluster, make sure the client of the connection can reach the head node of the cluster through its private IP address\. If the client isn't in the same VPC as the head node, use a proxy instance in a public subnet of the VPC\. This requirement applies to both SSH and DCV connections\. The public IP of a head node isn't accessible if the subnet doesn't have internet access\. The `pcluster ssh` and `dcv-connect` commands use the public IP if it exists or the private IP\. Before you create your cluster, verify that [auto\-assign public IPv4 address is disabled](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip) in the subnet to ensure that the `pcluster` commands have access to the cluster\.

  The following example shows how you can connect to a DCV session running in the head node of your cluster\. You connect through a proxy EC2 instance\. The instance functions as a NICE DCV server for your PC and as the client for the head node in the private subnet\.

  Connect over DCV through a proxy instance in a public subnet:

  1. Create an EC2 instance in a public subnet, which is in the same VPC as the cluster’s subnet\.

  1. Ensure that the NICE DCV client and server are installed on your EC2 instance\.

  1. Attach an AWS ParallelCluster User Policy to the proxy EC2 instance\. For more information, see [AWS ParallelCluster example ` pcluster` user policies](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies)\.

  1. Install AWS ParallelCluster on the proxy EC2 instance\.

  1. Connect over DCV to the proxy EC2 instance\.

  1. Use the `pcluster dcv-connect` command on the proxy instance to connect to the cluster inside the subnet without internet access\.
+ *Interacting with other AWS services:* Only services strictly required by AWS ParallelCluster are listed above\. If your cluster must interact with other services, create the corresponding VPC endpoints\.