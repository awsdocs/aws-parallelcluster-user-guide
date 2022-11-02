# Creating a cluster with Slurm accounting<a name="tutorials_07_slurm-accounting-v3"></a>

Learn how to configure and create a cluster with Slurm accounting\. For more information, see [Slurm accounting with AWS ParallelCluster](slurm-accounting-v3.md)\.

In this tutorial you'll use a [CloudFormation quick create template](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=pcluster-slurm-db&templateURL=https://us-east-1-aws-parallelcluster.s3.amazonaws.com/templates/1-click/serverless-database.yaml) to create an [Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html) for MySQL serverless database\. The template instructs CloudFormation to create all the necessary components to deploy an Amazon Aurora serverless database on the same VPC as the cluster\. It also creates a basic networking and security configuration for the connection between the cluster and the database\.

**Note**  
The provided quick create template serves as an example and doesn't cover all possible use cases for a Slurm accounting database server\. It's the responsibility of customers to create a database server with the configuration and capacity appropriate for their production workloads\.

**Prerequisites**
+ AWS ParallelCluster [is installed](install-v3-parallelcluster.md)\.
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
+ You have an [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ You have an IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) required to run the [`pcluster`](pcluster-v3.md) CLI\.

## Step 1: Create the VPC and subnets for AWS ParallelCluster<a name="slurm-accounting-vpc-v3"></a>

To use the provided CloudFormation template for the Slurm accounting database, users must have the VPC for the cluster ready\. You can do this manually or as part of the [Configuring AWS ParallelCluster](install-v3-configuring.md) procedure\. If you have already used AWS ParallelCluster in the past, you might have a VPC ready for the deployment of the cluster and the database server\.

## Step 2: Create the database stack<a name="slurm-accounting-db-stack-v3"></a>

Use the [CloudFormation quick create template](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=pcluster-slurm-db&templateURL=https://us-east-1-aws-parallelcluster.s3.amazonaws.com/templates/1-click/serverless-database.yaml) to create a database stack for Slurm accounting\. The template requires following inputs:
+ Database server credentials: admin username and password\.
+ Sizing of the Amazon Aurora serverless cluster\. This depends on the expected cluster loading\.
+ Networking parameters: target VPC and subnets or CIDR blocks for the creation of the subnets\.

Select appropriate credentials and size for your database server\. For the networking options, you're required to use the same VPC that the AWS ParallelCluster cluster is deployed to\. You can create the subnets for the database and pass them as input to the template\. Or, provide two disjoint CIDR blocks for the two subnets and let the CloudFormation template create the two subnets for CIDR blocks\. In this case, make sure the CIDR blocks don't overlap with existing subnets\. If the CIDR blocks overlap with existing subnets, stack creation fails\.

The creation of the database server can take several minutes\.

## Step 3: Create a cluster with Slurm accounting enabled<a name="slurm-accounting-create-cluster-v3"></a>

The provided CloudFormation template generates a CloudFormation stack with some defined outputs\. You can view the outputs in the **Outputs** tab in the CloudFormation stack view from the AWS Management Console\. Some of these outputs must be used in the AWS ParallelCluster cluster configuration file to enable the Slurm accounting functionality:
+ `DatabaseHost`: to be used for the [`SlurmSettings`](Scheduling-v3.md#Scheduling-v3-SlurmSettings) / [`Database`](Scheduling-v3.md#Scheduling-v3-SlurmSettings-Database) / [`Uri`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Database-Uri) cluster config parameter\.
+ `DatabaseAdminUser`: to be used for the [`SlurmSettings`](Scheduling-v3.md#Scheduling-v3-SlurmSettings) / [`Database`](Scheduling-v3.md#Scheduling-v3-SlurmSettings-Database) / [`UserName`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Database-UserName) cluster configuration parameter value\.
+ `DatabaseSecretArn`: to be used for the [`SlurmSettings`](Scheduling-v3.md#Scheduling-v3-SlurmSettings) / [`Database`](Scheduling-v3.md#Scheduling-v3-SlurmSettings-Database) / [`PasswordSecretArn`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Database-PasswordSecretArn) cluster config parameter\.
+ `DatabaseClientSecurityGroup`: this is the security group to be attached to the head node of the cluster defined in the [`HeadNode`](HeadNode-v3.md) / [`Networking`](HeadNode-v3.md#HeadNode-v3-Networking) / [`SecurityGroups`](HeadNode-v3.md#yaml-HeadNode-Networking-SecurityGroups) configuration parameter\.

Update your cluster configuration file `Database` parameters with the output values and use the [`pcluster`](pcluster-v3.md) CLI to create the cluster\.

```
$ pcluster create-cluster -n cluster-3.x -c path/to/cluster-config.yaml
```

After the cluster is successfully created, you can start using Slurm accounting commands such as `sacctmgr` or `sacct`\.