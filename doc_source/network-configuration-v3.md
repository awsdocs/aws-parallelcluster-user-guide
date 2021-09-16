# Network configurations<a name="network-configuration-v3"></a>

AWS ParallelCluster uses Amazon Virtual Private Cloud \(VPC\) for networking\. VPC provides a flexible and configurable networking platform where you can deploy clusters\.

 The VPC must have `DNS Resolution = yes`, `DNS Hostnames = yes` and DHCP options with the correct domain\-name for the Region\. The default DHCP Option Set already specifies the required *AmazonProvidedDNS*\. If specifying more than one domain name server, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

AWS ParallelCluster supports the following high\-level configurations:
+  One subnet for both head and compute nodes\. 
+  Two subnets, with the head node in one public subnet, and compute nodes in a private subnet\. The subnets can be either new or existing ones\. 

 All of these configurations can operate with or without public IP addressing\. AWS ParallelCluster can also be deployed to use an HTTP proxy for all AWS requests\. The combinations of these configurations result in many deployment scenarios\. For example, you can configure a single public subnet with all access over the internet\. Or, you can configure a fully private network using AWS Direct Connect and HTTP proxy for all traffic\.

Starting from AWS ParallelCluster 3\.0\.0 it is possible to configure different `SecurityGroups`, `AdditionalSecurityGroups` and `PlacementGroup` settings for each queue, see `HeadNode / Networking` \([`Networking`](HeadNode-v3.md#HeadNode-v3-Networking)\) and `SlurmQueues / Networking` \([`SlurmQueues` Properties](Scheduling-v3.md#Scheduling-v3-SlurmQueues.properties)\) and `AwsBatchQueues / Networking` \([`AwsBatchQueues` Properties](Scheduling-v3.md#Scheduling-v3-AwsBatchQueues.properties)\) sections for more details\.

See the following architecture diagrams for illustrations of some networking scenarios:

## AWS ParallelCluster in a single public subnet<a name="network-configuration-v3-single-subnet"></a>

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/single-public-subnet.png) 

 The configuration for this architecture requires the following settings:

```
# Note that all values are only provided as examples
...
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

In this configuration all instances of the cluster need to be assigned a public IP in order to get internet access\. To achieve this do the following:
+  Make sure the head node is assigned a public IP by either turning on “Enable auto\-assign public IPv4 address“ setting for the subnet used in `HeadNode > Networking > SubnetId` or by assigning an Elastic Ip in `HeadNode > Networking > ElasticIp`\. 
+  Make sure the compute nodes are assigned a public IP by either turning on “Enable auto\-assign public IPv4 address“ setting for the subnet used in `Scheduling > SlurmQueues > Networking > SubnetIds` or by setting `AssignPublicIp: true` in `Scheduling > SlurmQueues > Networking`\. 

For more information, see [ Enabling internet access](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#vpc-igw-internet-access) in *Amazon VPC User Guide*\.

## AWS ParallelCluster using two subnets<a name="network-configuration-v3-two-subnets"></a>

 

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/two-subnets.png) 

 The configuration to use an existing private subnet for compute instances requires the following settings:

```
# Note that all values are only provided as examples
...
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

In this configuration only the head node of the cluster is required to have a public IP assigned\. You can achieve so by either turning on “Enable auto\-assign public IPv4 address“ setting for the subnet used in `HeadNode > Networking > SubnetId` or by assigning an Elastic IP in `HeadNode > Networking > ElasticIp`\.

This configuration requires a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an internal proxy in the subnet used for the queues, to give internet access to the compute instances\.

## AWS ParallelCluster in a single private subnet connected using AWS Direct Connect<a name="network-configuration-v3-single-subnet-direct-connect"></a>

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/single-private-DirectConnect.png) 

 The configuration for this architecture requires the following settings:

```
# Note that all values are only provided as examples
...
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

 When `Scheduling / SlurmQueues / Networking / AssignPublicIp` is set to `false`, the subnets must be correctly set up to use the Proxy for all traffic\. Web access is required for both head and compute nodes\. 

 

## AWS ParallelCluster with AWS Batch scheduler<a name="network-configuration-v3-batch"></a>

 When you use `awsbatch` as the scheduler type, AWS ParallelCluster creates an AWS Batch managed compute environment\. The AWS Batch environment takes care of managing Amazon Elastic Container Service \(Amazon ECS\) container instances, which are launched in the subnet configured in the `AwsBatchQueues > Networking > SubnetIds` parameter\. For AWS Batch to function correctly, Amazon ECS container instances need external network access to communicate with the Amazon ECS service endpoint\. This translates into the following scenarios: 
+  The Subnet Id specified for the queue uses a NAT gateway to access the internet\. \(We recommended this approach\.\) 
+  Instances launched in the queue subnet have public IP addresses and can reach the internet through an Internet Gateway\. 

Additionally, if you're interested in multi\-node parallel jobs \(from the [AWS Batch docs](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html#mnp-ce)\):

AWS Batch multi\-node parallel jobs use the Amazon ECS `awsvpc` network mode, which gives your multi\-node parallel job containers the same networking properties as Amazon EC2 instances\. Each multi\-node parallel job container gets its own elastic network interface, a primary private IP address, and an internal DNS hostname\. The network interface is created in the same Amazon VPC subnet as its host compute resource\. Any security groups that are applied to your compute resources are also applied to it\.

When using Amazon ECS Task Networking, the `awsvpc` network mode doesn't provide elastic network interfaces with public IP addresses for tasks that use the Amazon EC2 launch type\. To access the internet, tasks that use the Amazon EC2 launch type must be launched in a private subnet that's configured to use a NAT gateway\.

You must configure a NAT gateway in order to enable the cluster to run multi\-node parallel jobs\.

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/two-subnets-batch.png) 

 All the previous configuration and considerations are valid for AWS Batch, too\. See an example of AWS Batch networking configuration\. *Note that all values are only provided as examples\.*

```
...
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

In the `Scheduling > AwsBatchQueues > Networking` section the `SubnetIds` is a list type but, currently, only one subnet is supported\.

For more information, see the following topics:
+  [AWS Batch managed compute environments](https://docs.aws.amazon.com/batch/latest/userguide/compute_environments.html#managed_compute_environments) 
+  [AWS Batch multi\-node parallel jobs](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html) 
+  [Amazon ECS task networking with the awsvpc network mode](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) 