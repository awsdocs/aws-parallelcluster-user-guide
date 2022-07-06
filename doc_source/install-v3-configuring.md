# Configuring AWS ParallelCluster<a name="install-v3-configuring"></a>

After you install AWS ParallelCluster, complete the following configuration steps\.

Verify that your AWS Account has a role that includes the permissions needed to run the [`pcluster`](pcluster-v3.md) CLI\. For more information, see [AWS ParallelCluster example user policies](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies)\.

Set up your AWS credentials\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS CLI user guide*\.

```
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [us-east-1]: us-east-1
Default output format [None]:
```

The AWS Region where the cluster is launched must have at least one Amazon EC2 key pair\. For more information, see [Amazon EC2 key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
$ pcluster configure --config cluster-config.yaml
```

The configure wizard prompts you for all of the information that's required to create your cluster\. The details of the sequence differ when using AWS Batch as the scheduler compared to using Slurm\.

------
#### [ Slurm ]

From the list of valid AWS Region identifiers, choose the Region where you want your cluster to run\.

**Note**  
The list of Regions shown is based on the partition of your account, and only includes Regions that are enabled for your account\. For more information about enabling Regions for your account, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *AWS General Reference*\. The example shown is from the AWS Global partition\. If your account is in the AWS GovCloud \(US\) partition, only Regions in that partition are listed \(`gov-us-east-1` and `gov-us-west-1`\)\. Similarly, if your account is in the AWS China partition, only `cn-north-1` and `cn-northwest-1` are shown\. For the complete list of Regions supported by AWS ParallelCluster, see [Supported Regions for AWS ParallelCluster version 3](supported-regions-v3.md)\.

```
Allowed values for AWS Region ID:
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

The key pair is selected from the key pairs that are registered with Amazon EC2 in the selected Region\. Choose the key pair:

```
Allowed values for EC2 Key Pair Name:
1. your-key-1
2. your-key-2
EC2 Key Pair Name [your-key-1]:
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

Choose head node instance type:

```
Head node instance type [t2.micro]:
```

Choose the queue configuration\. Note: Instance type can't be specified for multiple compute resources in the same queue\.

```
Number of queues [1]:
Name of queue 1 [queue1]:
Number of compute resources for queue1 [1]: 2
Compute instance type for compute resource 1 in queue1 [t2.micro]:
Maximum instance count [10]:
Compute instance type for compute resource 2 in queue1 [t2.micro]: t3.micro
Maximum instance count [10]:
```

After the previous steps are completed, decide whether to use an existing VPC or let AWS ParallelCluster create a VPC for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the head and compute nodes in the same public subnet, or only the head node in a public subnet with all nodes in a private subnet\. It's possible to reach your quota for the number of VPCs allowed in a Region\. The default quota is five VPCs for a Region\. For more information about this quota and how to request an increase, see [VPC and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

If you let AWS ParallelCluster create a VPC, you must decide if all nodes should be in a public subnet\.

**Important**  
VPCs created by AWS ParallelCluster do not enable VPC Flow Logs by default\. VPC Flow Logs enable you to capture information about the IP traffic going to and from network interfaces in your VPCs\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

```
Automate VPC creation? (y/n) [n]: y
Allowed values for Availability Zone:
1. us-east-1a
2. us-east-1b
3. us-east-1c
4. us-east-1d
5. us-east-1e
6. us-east-1f
Availability Zone [us-east-1a]:
Allowed values for Network Configuration:
1. Head node in a public subnet and compute fleet in a private subnet
2. Head node and compute fleet in the same public subnet
Network Configuration [Head node in a public subnet and compute fleet in a private subnet]: 1
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
#### [ AWS Batch ]

From the list of valid AWS Region identifiers, choose the Region where you want your cluster to run\.

**Note**  
The list of Regions shown is based on the partition of your account\. It only includes Regions that are enabled for your account\. For more information about enabling Regions for your account, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *AWS General Reference*\. The example shown is from the AWS Global partition\. If your account is in the AWS GovCloud \(US\) partition, only Regions in that partition are listed \(`gov-us-east-1` and `gov-us-west-1`\)\. Similarly, if your account is in the AWS China partition, only `cn-north-1` and `cn-northwest-1` are shown\. For the complete list of Regions supported by AWS ParallelCluster, see [Supported Regions for AWS ParallelCluster version 3](supported-regions-v3.md)\.

```
Allowed values for AWS Region ID:
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
AWS Region ID [us-east-1]:
```

The key pair is selected from the key pairs registered with Amazon EC2 in the selected Region\. Choose the key pair:

```
Allowed values for EC2 Key Pair Name:
1. your-key-1
2. your-key-2
EC2 Key Pair Name [your-key-1]:
```

Choose the scheduler to use with your cluster\.

```
Allowed values for Scheduler:
1. slurm
2. awsbatch
Scheduler [slurm]: 2
```

When `awsbatch` is selected as the scheduler, `alinux2` is used as the operating system\. The head node instance type is entered:

```
Head node instance type [t2.micro]:
```

Choose the queue configuration\. The AWS Batch scheduler only contains a single queue\. The maximum size of the cluster of compute nodes is entered\. This is measured in vCPUs\.

```
Number of queues [1]:
Name of queue 1 [queue1]:
Maximum vCPU [10]:
```

Decide whether to use existing VPCs or let AWS ParallelCluster create VPCs for you\. If you don't have a properly configured VPC, AWS ParallelCluster can create a new one\. It either uses both the head and compute nodes in the same public subnet, or only the head node in a public subnet with all nodes in a private subnet\. It's possible to reach your quota on the number of VPCs allowed in a Region\. The default number of VPCs is five\. For more information about this quota and how to request an increase, see [VPC and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-vpcs-subnets) in the *Amazon VPC User Guide*\.

**Important**  
VPCs created by AWS ParallelCluster do not enable VPC Flow Logs by default\. VPC Flow Logs enable you to capture information about the IP traffic going to and from network interfaces in your VPCs\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

If you let AWS ParallelCluster create a VPC, make sure that you decide whether all nodes are to be in a public subnet\.

```
Automate VPC creation? (y/n) [n]: y
Allowed values for Availability Zone:
1. us-east-1a
2. us-east-1b
3. us-east-1c
4. us-east-1d
5. us-east-1e
6. us-east-1f
Availability Zone [us-east-1a]:
Allowed values for Network Configuration:
1. Head node in a public subnet and compute fleet in a private subnet
2. Head node and compute fleet in the same public subnet
Network Configuration [Head node in a public subnet and compute fleet in a private subnet]: *1*
Beginning VPC creation. Please do not leave the terminal until the creation is finalized
```

If you don't create a new VPC, you must select an existing VPC\.

If you choose to have AWS ParallelCluster create the VPC, make a note of the VPC ID so you can use the AWS CLI or AWS Management Console to delete it later\.

```
Automate VPC creation? (y/n) [n]: n
Allowed values for VPC ID:
  #  id                     name                                 number_of_subnets
---  ---------------------  ---------------------------------  -------------------
  1  vpc-0b4ad9c4678d3c7ad  ParallelClusterVPC-20200118031893                    2
  2  vpc-0e87c753286f37eef  ParallelClusterVPC-20191118233938                    5
VPC ID [vpc-0b4ad9c4678d3c7ad]: 1
```

After the VPC has been selected, make sure that you decide whether to use existing subnets or create new ones\.

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
+ The VPC must also have DHCP options with the correct `domain-name` for the Region\. The default DHCP Option Set already specifies the required AmazonProvidedDNS\. If specifying more than one domain name server, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. When using private subnets, use a NAT gateway or an internal proxy to enable web access for compute nodes\. For more information, see [Network configurations](network-configuration-v3.md)\.

When all settings contain valid values, you can launch the cluster by running the create command\.

```
$ pcluster create-cluster --cluster-name test-cluster --cluster-configuration cluster-config.yaml
{
  "cluster": {
    "clusterName": "test-cluster",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:xxx:stack/test-cluster/abcdef0-f678-890a-5abc-021345abcdef",
    "region": "eu-west-1",
    "version": "3.1.4",
    "clusterStatus": "CREATE_IN_PROGRESS"
  },
  "validationMessages": []
}
```

 Follow cluster progress: 

```
$ pcluster describe-cluster --cluster-name test-cluster
```

 or 

```
$ pcluster list-clusters --query 'clusters[?clusterName==`test-cluster`]'
```

After the cluster reaches the `"clusterStatus": "CREATE_COMPLETE"` status, you can connect to it by using your normal SSH client settings\. For more information about connecting to Amazon EC2 instances, see the [EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux) in the *Amazon EC2 User Guide for Linux Instances*\. Or you can connect the cluster through 

```
$ pcluster ssh --cluster-name test-cluster -i ~/path/to/keyfile.pem
```

To delete the cluster, run the following command\.

```
$ pcluster delete-cluster --region us-east-1 --cluster-name test-cluster
```

After the cluster is deleted, you can delete the network resources in the VPC by deleting the CloudFormation networking stack\. The stack's name starts with "parallelclusternetworking\-" and contains the creation time in "YYYYMMDDHHMMSS" format\. You can list the stacks using the [https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/ListStacks) command\.

```
$ aws --region us-east-1 cloudformation list-stacks \
   --stack-status-filter "CREATE_COMPLETE" \
   --query "StackSummaries[].StackName" | \
   grep -e "parallelclusternetworking-"
   "parallelclusternetworking-pubpriv-20191029205804"
```

 The stack can be deleted using the [https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/DeleteStack](https://docs.aws.amazon.com/goto/aws-cli/cloudformation-2010-05-15/DeleteStack) command\.

```
$ aws --region us-east-1 cloudformation delete-stack \
   --stack-name parallelclusternetworking-pubpriv-20191029205804
```

The VPC that [`pcluster configure`](pcluster.configure-v3.md) creates for you *isn't* created in the CloudFormation networking stack\. You can delete that VPC manually in the console or by using the AWS CLI\.

```
$ aws --region us-east-1 ec2 delete-vpc --vpc-id vpc-0b4ad9c4678d3c7ad
```