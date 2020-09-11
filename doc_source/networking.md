# Network configurations<a name="networking"></a>

AWS ParallelCluster uses Amazon Virtual Private Cloud \(VPC\) for networking\. VPC provides a flexible and configurable networking platform in which to deploy clusters\.

The VPC must have `DNS Resolution = yes`, `DNS Hostnames = yes` and DHCP options with the correct domain\-name for the Region\. The default DHCP Option Set already specifies the required *AmazonProvidedDNS*\. If specifying more than one domain name server, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

AWS ParallelCluster supports the following high\-level configurations:
+ One subnet for both head and compute nodes\.
+ Two subnets, with the head node in one public subnet, and compute nodes in a private subnet\. The subnets can be new or existing\.

All of these configurations can operate with or without public IP addressing\. AWS ParallelCluster can also be deployed to use an HTTP proxy for all AWS requests\. The combinations of these configurations result in many deployment scenarios\. For example, you can configure a single public subnet with all access over the internet\., Or you can configure a fully private network using AWS Direct Connect and HTTP proxy for all traffic\.

See the following architecture diagrams for illustrations of some of these scenarios:

## AWS ParallelCluster in a single public subnet<a name="aws-parallelcluster-in-a-single-public-subnet"></a>

The configuration for this architecture requires the following settings:

```
[vpc public]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-<public>
use_public_ips = true
```

The [`use_public_ips`](vpc-section.md#use-public-ips) setting cannot be set to `false`, because the internet gateway requires that all instances have a globally unique IP address\. For more information, see [Enabling internet access](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#vpc-igw-internet-access) in *Amazon VPC User Guide*\.

## AWS ParallelCluster using two subnets<a name="aws-parallelcluster-using-two-subnets"></a>

![\[AWS ParallelCluster using two subnets\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/networking_two_subnets.jpg)

The configuration to create a new private subnet for compute instances requires the following settings:

 *Note that all values are examples only* 

```
[vpc public-private-new]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-<public>
compute_subnet_cidr = 10.0.1.0/24
```

The configuration to use an existing private network requires the following settings:

```
[vpc public-private-existing]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-<public>
compute_subnet_id = subnet-<private>
```

Both of these configurations require a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an internal proxy to enable web access for compute instances\.

## AWS ParallelCluster in a single private subnet connected using AWS Direct Connect<a name="aws-parallelcluster-in-a-single-private-subnet-connected-using-direct-connect"></a>

![\[Private AWS ParallelCluster with AWS Direct Connect\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/networking_private_dx.jpg)

The configuration for this architecture requires the following settings:

```
[cluster private-proxy]
proxy_server = http://proxy.corp.net:8080

[vpc private-proxy]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-<private>
use_public_ips = false
```

When `use_public_ips` is set to `false`, the VPC must be correctly set up to use the Proxy for all traffic\. Web access is required for both head and compute nodes\.

## AWS ParallelCluster with `awsbatch` scheduler<a name="awsbatch-networking"></a>

When you use `awsbatch` as the scheduler type, AWS ParallelCluster creates an AWS Batch managed compute environment\. The AWS Batch environment takes care of managing Amazon Elastic Container Service \(Amazon ECS\) container instances, which are launched in the `compute_subnet`\. In order for AWS Batch to function correctly, Amazon ECS container instances need external network access to communicate with the Amazon ECS service endpoint\. This translates into the following scenarios:
+ The `compute_subnet` uses a NAT Gateway to access the internet\. \(We recommended this approach\.\)
+ Instances launched in the `compute_subnet` have public IP addresses and can reach the internet through an Internet Gateway\.

Additionally, if you are interested in multi\-node parallel jobs \(from the [AWS Batch docs ](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html#mnp-ce)\):

AWS Batch multi\-node parallel jobs use the Amazon ECS `awsvpc` network mode, which gives your multi\-node parallel job containers the same networking properties as Amazon EC2 instances\. Each multi\-node parallel job container gets its own elastic network interface, a primary private IP address, and an internal DNS hostname\. The network interface is created in the same Amazon VPC subnet as its host compute resource\. Any security groups that are applied to your compute resources are also applied to it\.

When using Amazon ECS Task Networking, the awsvpc network mode does not provide elastic network interfaces with public IP addresses for tasks that use the Amazon EC2 launch type\. To access the internet, tasks that use the Amazon EC2 launch type must be launched in a private subnet that is configured to use a NAT Gateway\.

This leaves us with the only option, to configure a NAT Gateway in order to enable the cluster to execute multi\-node parallel jobs\.

![\[AWS ParallelCluster networking with awsbatch scheduler\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/networking_batch.jpg)

For more information, see the following AWS documents:
+  [AWS Batch managed compute environments](https://docs.aws.amazon.com/batch/latest/userguide/compute_environments.html#managed_compute_environments) 
+  [AWS Batch multi\-node parallel jobs](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html) 
+  [Amazon ECS task networking with the `awsvpc` network mode](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) 