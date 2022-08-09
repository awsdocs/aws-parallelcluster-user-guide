# Configuring AWS ParallelCluster<a name="getting-started-configuring-parallelcluster"></a>

After you install AWS ParallelCluster, complete the following configuration steps\.

Verify that your AWS Account has a role that includes the permissions needed to run the [`pcluster`](pcluster.md) CLI\. For more information, see [AWS ParallelCluster example instance and user policies](iam.md#example-parallelcluser-policies)\.

Set up your AWS credentials\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS CLI user guide*\.

```
$ aws configure
  AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [us-east-1]: us-east-1
Default output format [None]:
```

The Region where the cluster is launched must have at least one Amazon EC2 key pair\. For more information, see [Amazon EC2 key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
$ pcluster configure
```

The configure wizard prompts you for all of the information that's needed to create your cluster\. The details of the sequence differ when using AWS Batch as the scheduler compared to using Slurm\. For more information about a cluster configuration, see [Configuration](configuration.md)\.

**Note**  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\. You can continue using them in versions up to and including 2\.11\.4, but they aren't eligible for future updates or troubleshooting support from the AWS service and AWS Support teams\.

------
#### [ Slurm ]

From the list of valid AWS Region identifiers, choose the Region where you want your cluster to run\.

**Note**  
The list of Regions shown is based on the partition of your account, and only includes Regions that are enabled for your account\. For more information about enabling Regions for your account, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *AWS General Reference*\. The example shown is from the AWS Global partition\. If your account is in the AWS GovCloud \(US\) partition, only Regions in that partition are listed \(`gov-us-east-1` and `gov-us-west-1`\)\. Similarly, if your account is in the AWS China partition, only `cn-north-1` and `cn-northwest-1` are shown\. For the complete list of Regions supported by AWS ParallelCluster, see [Supported Regions](supported-regions.md)\.

```
Allowed values for the AWS Region ID:
1. af-south-1
2. ap-east-1
3. ap-northeast-1
4. ap-northeast-2
5. ap-south-1
6. ap-southeast-1
7. ap-southeast-2
8. ca-central-1
9. eu-central-1
10. eu-north-1
11. eu-south-1
12. eu-west-1
13. eu-west-2
14. eu-west-3
15. me-south-1
16. sa-east-1
17. us-east-1
18. us-east-2
19. us-west-1
20. us-west-2
AWS Region ID [ap-northeast-1]:
```

Choose the scheduler to use with your cluster\.

```
Allowed values for Scheduler:
1. slurm
2. awsbatch
Scheduler [slurm]:
```

Choose the operating system\.

```
Allowed values for Operating System:
1. alinux2
2. centos7
3. ubuntu1804
4. ubuntu2004
Operating System [alinux2]:
```

**Note**  
Support for `alinux2` was added in AWS ParallelCluster version 2\.6\.0\.

The minimum and maximum size of the cluster of compute nodes is entered\. This is measured in number of instances\.

```
Minimum cluster size (instances) [0]:
Maximum cluster size (instances) [10]:
```

The head and compute nodes instance types are entered\. For instance types, your account instance limits are large enough to meet your requirements\. For more information, see [On\-Demand Instance limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html#ec2-on-demand-instances-limits) in the *Amazon EC2 User Guide for Linux Instances*\.

```
Master instance type [t2.micro]: 
Compute instance type [t2.micro]:
```

The key pair is selected from the key pairs registered with Amazon EC2 in the selected Region\.

```
Allowed values for EC2 Key Pair Name:
1. prod-uswest1-key
2. test-uswest1-key
EC2 Key Pair Name [prod-uswest1-key]:
```

After the previous steps are completed, decide whether to use an existing VPC or let AWS ParallelCluster create a VPC for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the head and compute nodes in the same public subnet, or only the head node in a public subnet with all nodes in a private subnet\. It's possible to reach your limit on number of VPCs in a Region\. The default limit is five VPCs for each Region\. For more information about this limit and how to request an increase, see [VPC and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

If you let AWS ParallelCluster create a VPC, you must decide if all nodes should be in a public subnet\.

**Important**  
VPCs created by AWS ParallelCluster do not enable VPC Flow Logs by default\. VPC Flow Logs enable you to capture information about the IP traffic going to and from network interfaces in your VPCs\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

```
Automate VPC creation? (y/n) [n]: y
Allowed values for Network Configuration:
1. Master in a public subnet and compute fleet in a private subnet
2. Master and compute fleet in the same public subnet
Network Configuration [Master in a public subnet and compute fleet in a private subnet]: 1
Beginning VPC creation. Please do not leave the terminal until the creation is finalized
```

If you don't create a new VPC, you must select an existing VPC\.

If you choose to have AWS ParallelCluster create the VPC, make a note of the VPC ID so you can use the AWS CLI to delete it later\.

```
Automate VPC creation? (y/n) [n]: n
Allowed values for VPC ID:
  #  id                     name                                 number_of_subnets
---  ---------------------  ---------------------------------  -------------------
  1  vpc-0b4ad9c4678d3c7ad  ParallelClusterVPC-20200118031893                    2
  2  vpc-0e87c753286f37eef  ParallelClusterVPC-20191118233938                    5
VPC ID [vpc-0b4ad9c4678d3c7ad]: 1
```

After the VPC has been selected, you need to decide whether to use existing subnets or create new ones\.

```
Automate Subnet creation? (y/n) [y]: y
```

```
Creating CloudFormation stack...
Do not leave the terminal until the process has finished
```

------
#### [ AWS Batch ]

From the list of valid AWS Region identifiers, choose the Region where you want your cluster to run\.

```
Allowed values for AWS Region ID:
1. ap-northeast-1
2. ap-northeast-2
3. ap-south-1
4. ap-southeast-1
5. ap-southeast-2
6. ca-central-1
7. eu-central-1
8. eu-north-1
9. eu-west-1
10. eu-west-2
11. eu-west-3
12. sa-east-1
13. us-east-1
14. us-east-2
15. us-west-1
16. us-west-2
AWS Region ID [ap-northeast-1]:
```

Choose the scheduler to use with your cluster\.

```
Allowed values for Scheduler:
1. slurm
2. awsbatch
Scheduler [awsbatch]:
```

When `awsbatch` is selected as the scheduler, `alinux2` is used as the operating system\.

The minimum and maximum size of the cluster of compute nodes is entered\. This is measured in vCPUs\.

```
Minimum cluster size (vcpus) [0]:
Maximum cluster size (vcpus) [10]:
```

The head node instance type is entered\. When using the `awsbatch` scheduler, the compute nodes use an instance type of `optimal`\.

```
Master instance type [t2.micro]: 
```

The Amazon EC2 key pair is selected from the key pairs registered with Amazon EC2 in the selected Region\.

```
Allowed values for EC2 Key Pair Name:
1. prod-uswest1-key
2. test-uswest1-key
EC2 Key Pair Name [prod-uswest1-key]:
```

Decide whether to use existing VPCs or let AWS ParallelCluster create VPCs for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the head and compute nodes in the same public subnet, or only the head node in a public subnet with all nodes in a private subnet\. It's possible to reach your limit on number of VPCs in a Region\. The default number of VPCs is five\. For more information about this limit and how to request an increase, see [VPC and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

**Important**  
VPCs created by AWS ParallelCluster do not enable VPC Flow Logs by default\. VPC Flow Logs enable you to capture information about the IP traffic going to and from network interfaces in your VPCs\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

If you let AWS ParallelCluster create a VPC, decide if all nodes should be in a public subnet\.

```
Automate VPC creation? (y/n) [n]: y
Allowed values for Network Configuration:
1. Master in a public subnet and compute fleet in a private subnet
2. Master and compute fleet in the same public subnet
Network Configuration [Master in a public subnet and compute fleet in a private subnet]: 1
Beginning VPC creation. Please do not leave the terminal until the creation is finalized
```

If you don't create a new VPC, you must select an existing VPC\.

If you choose to have AWS ParallelCluster create the VPC, make a note of the VPC ID so you can use the AWS CLI to delete it later\.

```
Automate VPC creation? (y/n) [n]: n
Allowed values for VPC ID:
  #  id                     name                                 number_of_subnets
---  ---------------------  ---------------------------------  -------------------
  1  vpc-0b4ad9c4678d3c7ad  ParallelClusterVPC-20200118031893                    2
  2  vpc-0e87c753286f37eef  ParallelClusterVPC-20191118233938                    5
VPC ID [vpc-0b4ad9c4678d3c7ad]: 1
```

After the VPC has been selected, decide whether to use existing subnets or create new ones\.

```
Automate Subnet creation? (y/n) [y]: y
```

```
Creating CloudFormation stack...
Do not leave the terminal until the process has finished
```

------

When you have completed the preceding steps, a simple cluster launches into a VPC\. The VPC uses an existing subnet that supports public IP addresses\. The route table for the subnet is `0.0.0.0/0 => igw-xxxxxx`\. Note the following conditions:
+ The VPC must have `DNS Resolution = yes` and `DNS Hostnames = yes`\.
+ The VPC should also have DHCP options with the correct `domain-name` for the Region\. The default DHCP Option Set already specifies the required AmazonProvidedDNS\. If specifying more than one domain name server, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. When using private subnets, use a NAT gateway or an internal proxy to enable web access for compute nodes\. For more information, see [Network configurations](networking.md)\.

When all settings contain valid values, you can launch the cluster by running the create command\.

```
$ pcluster create mycluster
```

After the cluster reaches the "CREATE\_COMPLETE" status, you can connect to it by using your normal SSH client settings\. For more information about connecting to Amazon EC2 instances, see the [EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux) in the *Amazon EC2 User Guide for Linux Instances*\.

To delete the cluster, run the following command\.

```
$ pcluster delete --region us-east-1 mycluster
```

To delete the network resources in the VPC, you can delete the CloudFormation networking stack\. The stack name starts with "parallelclusternetworking\-" and contains the creation time in "YYYYMMDDHHMMSS" format\. You can list the stacks using the [list\-stacks](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks) command\.

```
$ aws --region us-east-1 cloudformation list-stacks \
   --stack-status-filter "CREATE_COMPLETE" \
   --query "StackSummaries[].StackName" | \
   grep -e "parallelclusternetworking-"
    "parallelclusternetworking-pubpriv-20191029205804"
```

The stack can be deleted using the [delete\-stack](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/DeleteStack) command\.

```
$ aws --region us-east-1 cloudformation delete-stack \
   --stack-name parallelclusternetworking-pubpriv-20191029205804
```

The VPC that [`pcluster configure`](pcluster.configure-v3.md) creates for you is not created in the CloudFormation networking stack\. You can delete that VPC manually in the console or by using the AWS CLI\.

```
$ aws --region us-east-1 ec2 delete-vpc --vpc-id vpc-0b4ad9c4678d3c7ad
```