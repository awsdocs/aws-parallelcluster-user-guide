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

The Region where the cluster will be launched must have at least one Amazon EC2 key pair\. For more information, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
$ pcluster configure
```

The configure wizard prompts you for all of the information that's needed to create your cluster\. The details of the sequence differ when using AWS Batch as the scheduler compared to using SGE, Slurm, or Torque\.

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
2. centos6
3. centos7
4. ubuntu1604
5. ubuntu1804
Operating System [alinux]:
```

The minimum and maximum size of the cluster of compute nodes is entered\. This is measured in number of instances\.

```
Minimum cluster size (instances) [0]:
Maximum cluster size (instances) [10]:
```

The master and compute nodes instance types are entered\. For instance types, your account instance limits are large enough to meet your requirements\. For more information, see [On\-Demand Instance Limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html#ec2-on-demand-instances-limits) in the *Amazon EC2 User Guide for Linux Instances*\.

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

After the previous steps are completed, decide whether to use an existing VPC or let AWS ParallelCluster create a VPC for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the master and compute nodes in the same public subnet, or only the master node in a public subnet with all nodes in a private subnet\. It is possible to reach your limit on number of VPCs in a Region\. The default limit is five VPCs per Region\. For more information about this limit and how to request an increase, see [VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

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
```

```
Automate VPC creation? (y/n) [n]: n
Allowed values for VPC ID:
1. subnet-0b4ad9c4678d3c7ad
2. vpc-0e87c753286f37eef | ParallelClusterVPC-20191118233938 | 5 subnets inside
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

When `awsbatch` is selected as the scheduler, `alinux` is used as the operating system\.

The minimum and maximum size of the cluster of compute nodes is entered\. This is measured in vCPUs\.

```
Minimum cluster size (vcpus) [0]:
Maximum cluster size (vcpus) [10]:
```

The master node instance type is entered\. When using the `awsbatch` scheduler, the compute nodes use an instance type of `optimal`\.

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

Decide whether to use existing VPCs or let AWS ParallelCluster create VPCs for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the master and compute nodes in the same public subnet, or only the master node in a public subnet with all nodes in a private subnet\. It is possible to reach your limit on number of VPCs in a Region\. The default number of VPCs is five\. For more information about this limit and how to request an increase, see [VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

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
```

```
Automate VPC creation? (y/n) [n]: n
Allowed values for VPC ID:
1. subnet-0b4ad9c4678d3c7ad
2. vpc-0e87c753286f37eef | ParallelClusterVPC-20191118233938 | 5 subnets inside
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
+ The VPC should also have DHCP options with the correct `domain-name` for the region\. The default DHCP Option Set already specifies the required *AmazonProvidedDNS*\. If specifying more than one domain name server, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

When all settings contain valid values, you can launch the cluster by running the create command\.

```
$ pcluster create mycluster
```

After the cluster reaches the "CREATE\_COMPLETE" status, you can connect to it by using your normal SSH client settings\. For more details on connecting to Amazon EC2 instances, see the [EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux) in the *Amazon EC2 User Guide for Linux Instances*\.
