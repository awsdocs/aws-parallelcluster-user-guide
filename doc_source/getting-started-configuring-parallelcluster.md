# Configuring AWS ParallelCluster<a name="getting-started-configuring-parallelcluster"></a>

After you install AWS ParallelCluster, complete the following configuration steps\.

First, set up your AWS credentials\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS CLI user guide*\. 

```
$ aws configure
  AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [us-east-1]: us-east-1
Default output format [None]:
```

The Region where the cluster will be launched must have at least one Amazon EC2 key pair\. For more information, see [Amazon EC2 key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
$ pcluster configure
```

The configure wizard prompts you for all of the information that's needed to create your cluster\. The details of the sequence differ when using AWS Batch as the scheduler compared to using SGE, Slurm, or Torque\.

**Warning**  
Starting on December 31, 2021, AWS will no longer include SGE and Torque support for all released versions of AWS ParallelCluster\. Previous versions of AWS ParallelCluster that support SGE and Torque will still be available for download and use\. However, these versions will not be eligible for future updates or troubleshooting support from AWS service and customer support teams\. Moreover, future releases of AWS ParallelCluster made before and after December 31, 2021 will not include support for either SGE or Torque\.

------
#### [ SGE, Slurm, or Torque ]

From the list of valid AWS Region identifiers, choose the Region in which you want your cluster to run\.

```
Allowed values for the AWS Region ID:
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
1. sge
2. torque
3. slurm
4. awsbatch
Scheduler [sge]:
```

Choose the operating system\.

```
Allowed values for Operating System:
1. alinux
2. alinux2
3. centos6
4. centos7
5. ubuntu1604
6. ubuntu1804
Operating System [alinux]:
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

After the previous steps are completed, decide whether to use an existing VPC or let AWS ParallelCluster create a VPC for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the head and compute nodes in the same public subnet, or only the head node in a public subnet with all nodes in a private subnet\. It is possible to reach your limit on number of VPCs in a Region\. The default limit is five VPCs per Region\. For more information about this limit and how to request an increase, see [VPC and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

If you let AWS ParallelCluster create a VPC, you must decide whether all nodes should be in a public subnet\.

```
Automate VPC creation? (y/n) [n]: y
Allowed values for Network Configuration:
1. Master in a public subnet and compute fleet in a private subnet
2. Master and compute fleet in the same public subnet
Network Configuration [Master in a public subnet and compute fleet in a private subnet]: 1
Beginning VPC creation. Please do not leave the terminal until the creation is finalized
```

If you do not create a new VPC\. you must select an existing VPC\.

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
#### [ AWS Batch ]

From the list of valid AWS Region identifiers, choose the Region in which you want your cluster to run\.

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
1. sge
2. torque
3. slurm
4. awsbatch
Scheduler [sge]:
```

When `awsbatch` is selected as the scheduler, either `alinux` or `alinux2` can used as the operating system\.

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

Decide whether to use existing VPCs or let AWS ParallelCluster create VPCs for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the head and compute nodes in the same public subnet, or only the head node in a public subnet with all nodes in a private subnet\. It is possible to reach your limit on number of VPCs in a Region\. The default number of VPCs is five\. For more information about this limit and how to request an increase, see [VPC and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

If you let AWS ParallelCluster create a VPC, decide whether all nodes should be in a public subnet\.

```
Automate VPC creation? (y/n) [n]: y
Allowed values for Network Configuration:
1. Master in a public subnet and compute fleet in a private subnet
2. Master and compute fleet in the same public subnet
Network Configuration [Master in a public subnet and compute fleet in a private subnet]: 1
Beginning VPC creation. Please do not leave the terminal until the creation is finalized
```

If you do not create a new VPC\. you must select an existing VPC

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

When you have completed the preceding steps, a simple cluster launches into a VPC, using an existing subnet that supports public IP's \(the route table for the subnet is `0.0.0.0/0 => igw-xxxxxx`\)\. Note the following:
+ The VPC must have `DNS Resolution = yes` and `DNS Hostnames = yes`\.
+ The VPC should also have DHCP options with the correct `domain-name` for the Region\. The default DHCP Option Set already specifies the required AmazonProvidedDNS\. If specifying more than one domain name server, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. When using private subnets, use a NAT gateway or an internal proxy to enable web access for compute nodes\. For more information, see [Network configurations](networking.md)\.

When all settings contain valid values, you can launch the cluster by running the create command\.

```
$ pcluster create mycluster
```

After the cluster reaches the "CREATE\_COMPLETE" status, you can connect to it by using your normal SSH client settings\. For more details on connecting to Amazon EC2 instances, see the [EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux) in the *Amazon EC2 User Guide for Linux Instances*\.

If [`pcluster configure`](pcluster.configure.md) created a new VPC, you can delete that VPC by deleting the AWS CloudFormation stack it created\. The name will start with "parallelclusternetworking\-" and contain the creation time in a "YYYYMMDDHHMMSS" format\. You can list the stacks using the [list\-stacks](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks) command\.

```
$ aws --region us-east-2 cloudformation list-stacks \
   --stack-status-filter "CREATE_COMPLETE" \
   --query "StackSummaries[].StackName" | \
   grep -e "parallelclusternetworking-"
    "parallelclusternetworking-pubpriv-20191029205804"
```

The stack can be deleted using the [delete\-stack](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/DeleteStack) command\.

```
$ aws --region us-west-2 cloudformation delete-stack \
   --stack-name parallelclusternetworking-pubpriv-20191029205804
```
