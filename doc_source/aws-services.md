# AWS services used by AWS ParallelCluster<a name="aws-services"></a>

The following Amazon Web Services \(AWS\) services are used by AWS ParallelCluster\.

**Topics**
+ [AWS Auto Scaling](#aws-auto-scaling)
+ [AWS Batch](#aws-batch)
+ [AWS CloudFormation](#aws-services-cloudformation)
+ [Amazon CloudWatch](#amazon-cloudwatch)
+ [Amazon CloudWatch Logs](#amazon-cloudwatch-logs)
+ [AWS CodeBuild](#aws-codebuild)
+ [Amazon DynamoDB](#amazon-dynamodb)
+ [Amazon Elastic Block Store](#amazon-elastic-block-store-ebs)
+ [Amazon Elastic Compute Cloud](#amazon-ec2)
+ [Amazon Elastic Container Registry](#amazon-elastic-container-registry-ecr)
+ [Amazon EFS](#amazon-efs)
+ [Amazon FSx for Lustre](#amazon-fsx-for-lustre)
+ [AWS Identity and Access Management](#aws-identity-and-access-management-iam)
+ [AWS Lambda](#aws-lambda)
+ [NICE DCV](#nice-dcv)
+ [Amazon Route 53](#amazon-route-53)
+ [Amazon Simple Notification Service](#amazon-simple-notification-service-sns)
+ [Amazon Simple Queue Service](#amazon-simple-queuing-service-sqs)
+ [Amazon Simple Storage Service](#amazon-s3)
+ [Amazon VPC](#amazon-vpc)

## AWS Auto Scaling<a name="aws-auto-scaling"></a>

AWS Auto Scaling is a service that monitors your applications and automatically adjusts capacity based on your specific and changing service requirements\. This service manages your ComputeFleet instances as an Auto Scaling group\. The group can be elastically driven by your changing workload or statically fixed by your initial instance configurations\.

AWS Auto Scaling is used with ComputeFleet instances but is not used with AWS Batch clusters\.

For more information about AWS Auto Scaling, see [https://aws\.amazon\.com/autoscaling/](http://aws.amazon.com/autoscaling/) and [https://docs\.aws\.amazon\.com/autoscaling/](https://docs.aws.amazon.com/autoscaling/)\.

## AWS Batch<a name="aws-batch"></a>

AWS Batch is an AWS managed job scheduler service\. It dynamically provisions the optimal quantity and type of compute resources \(for example, CPU or memory\-optimized instances\) in AWS Batch clusters\. These resources are provisioned based on the specific requirements of your batch jobs, including volume requirements\. With AWS Batch, you don't need to install or manage additional batch computing software or server clusters to run your jobs effectively\.

AWS Batch is used only with AWS Batch clusters\.

For more information about AWS Batch, see [https://aws\.amazon\.com/batch/](http://aws.amazon.com/batch/) and [https://docs\.aws\.amazon\.com/batch/](https://docs.aws.amazon.com/batch/)\.

## AWS CloudFormation<a name="aws-services-cloudformation"></a>

AWS CloudFormation is an infrastructure\-as\-code service that provides a common language to model and provision AWS and third\-party application resources in your cloud environment\. It is the main service used by AWS ParallelCluster\. Each cluster in AWS ParallelCluster is represented as a stack, and all resources required by each cluster are defined within the AWS ParallelCluster AWS CloudFormation template\. In most cases, AWS ParallelCluster CLI commands directly correspond to AWS CloudFormation stack commands, such as create, update, and delete commands\. Instances that are launched within a cluster make HTTPS calls to the AWS CloudFormation endpoint in the Region where the cluster is launched\.

For more information about AWS CloudFormation, see [https://aws\.amazon\.com/cloudformation/](http://aws.amazon.com/cloudformation/) and [https://docs\.aws\.amazon\.com/cloudformation/](https://docs.aws.amazon.com/cloudformation/)\.

## Amazon CloudWatch<a name="amazon-cloudwatch"></a>

Amazon CloudWatch \(CloudWatch\) is a monitoring and observability service that provides you with data and actionable insights\. These insights can be used to monitor your applications, respond to performance changes and service exceptions, and optimize resource utilization\. In AWS ParallelCluster, CloudWatch is used for a dashboard, to monitor and log Docker image build steps and the output of the AWS Batch jobs\.

Before AWS ParallelCluster version 2\.10\.0, CloudWatch was used only with AWS Batch clusters\.

For more information about CloudWatch, see [https://aws\.amazon\.com/cloudwatch/](http://aws.amazon.com/cloudwatch/) and [https://docs\.aws\.amazon\.com/cloudwatch/](https://docs.aws.amazon.com/cloudwatch/)\.

## Amazon CloudWatch Logs<a name="amazon-cloudwatch-logs"></a>

Amazon CloudWatch Logs \(CloudWatch Logs\) is one of the core features of Amazon CloudWatch\. You can use it to monitor, store, view, and search the log files for many of the components used by AWS ParallelCluster\. 

Before AWS ParallelCluster version 2\.6\.0, CloudWatch Logs was only used with AWS Batch clusters\.

For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs.md)\.

## AWS CodeBuild<a name="aws-codebuild"></a>

AWS CodeBuild \(CodeBuild\) is an AWS managed continuous integration service that complies source code, runs tests, and produces software packages that are ready to deploy\. In AWS ParallelCluster, CodeBuild is used to automatically and transparently build Docker images when clusters are created\.

CodeBuild is used only with AWS Batch clusters\.

For more information about CodeBuild, see [https://aws\.amazon\.com/codebuild/](http://aws.amazon.com/codebuild/) and [https://docs\.aws\.amazon\.com/codebuild/](https://docs.aws.amazon.com/codebuild/)\.

## Amazon DynamoDB<a name="amazon-dynamodb"></a>

Amazon DynamoDB \(DynamoDB\) is a fast and flexible NoSQL database service\. It is used to store the minimal state information of the cluster\. The head node tracks provisioned instances in a DynamoDB table\.

DynamoDB is not used with AWS Batch clusters\.

For more information about DynamoDB, see [https://aws\.amazon\.com/dynamodb/](http://aws.amazon.com/dynamodb/) and [https://docs\.aws\.amazon\.com/dynamodb/](https://docs.aws.amazon.com/dynamodb/)\.

## Amazon Elastic Block Store<a name="amazon-elastic-block-store-ebs"></a>

Amazon Elastic Block Store \(Amazon EBS\) is a high\-performance block storage service that provides persistent storage for shared volumes\. All Amazon EBS settings can be passed through the configuration\. Amazon EBS volumes can either be initialized empty or from an existing Amazon EBS snapshot\.

For more information about Amazon EBS, see [https://aws\.amazon\.com/ebs/](http://aws.amazon.com/ebs/) and [https://docs\.aws\.amazon\.com/ebs/](https://docs.aws.amazon.com/ebs/)\.

## Amazon Elastic Compute Cloud<a name="amazon-ec2"></a>

Amazon Elastic Compute Cloud \(Amazon EC2\) provides the computing capacity for AWS ParallelCluster\. The head and compute nodes are Amazon EC2 instances\. Any instance type that support HVM can be selected\. The head and compute nodes can be different instance types\. Moreover, if multiple queues are used, some or all of compute nodes can also be launched as a Spot Instance\. Instance store volumes found on the instances are mounted as striped LVM volumes\.

For more information about Amazon EC2, see [https://aws\.amazon\.com/ec2/](http://aws.amazon.com/ec2/) and [https://docs\.aws\.amazon\.com/ec2/](https://docs.aws.amazon.com/ec2/)\.

## Amazon Elastic Container Registry<a name="amazon-elastic-container-registry-ecr"></a>

Amazon Elastic Container Registry \(Amazon ECR\) is a fully managed Docker container registry that makes it easy to store, manage, and deploy Docker container images\. In AWS ParallelCluster, Amazon ECR stores the Docker images that are built when clusters are created\. The Docker images are then used by AWS Batch to run the containers for the submitted jobs\.

Amazon ECR is used only with AWS Batch clusters\.

For more information, see [https://aws\.amazon\.com/ecr/](http://aws.amazon.com/ecr/) and [https://docs\.aws\.amazon\.com/ecr/](https://docs.aws.amazon.com/ecr/)\.

## Amazon EFS<a name="amazon-efs"></a>

Amazon Elastic File System \(Amazon EFS\) provides a simple, scalable, and fully managed elastic NFS file system for use with AWS Cloud services and on\-premises resources\. Amazon EFS is used when the [`efs_settings`](cluster-definition.md#efs-settings) setting is specified and refers to an [`[efs]` section](efs-section.md)\. Support for Amazon EFS was added in AWS ParallelCluster version 2\.1\.0\.

For more information about Amazon EFS, see [https://aws\.amazon\.com/efs/](http://aws.amazon.com/efs/) and [https://docs\.aws\.amazon\.com/efs/](https://docs.aws.amazon.com/efs/)\.

## Amazon FSx for Lustre<a name="amazon-fsx-for-lustre"></a>

Amazon FSx for Lustre provides a high\-performance file system that uses the open\-source Lustre file system\. Amazon FSx for Lustre is used when the [`fsx_settings`](cluster-definition.md#fsx-settings) setting is specified and refers to an [`[fsx]` section](fsx-section.md)\. Support for Amazon FSx for Lustre was added in AWS ParallelCluster version 2\.2\.1\.

For more information about Amazon FSx for Lustre, see [https://aws\.amazon\.com/fsx/lustre/](http://aws.amazon.com/fsx/lustre/) and [https://docs\.aws\.amazon\.com/fsx/](https://docs.aws.amazon.com/fsx/)\.

## AWS Identity and Access Management<a name="aws-identity-and-access-management-iam"></a>

AWS Identity and Access Management \(IAM\) is used within AWS ParallelCluster to provide a least privileged IAM role for Amazon EC2 for the instance that is specific to each individual cluster\. AWS ParallelCluster instances are given access only to the specific API calls that are required to deploy and manage the cluster\.

With AWS Batch clusters, IAM roles are also created for the components that are involved with the Docker image building process when clusters are created\. These components include the Lambda functions that are allowed to add and delete Docker images to and from the Amazon ECR repository\. They also include the functions allowed to delete the Amazon S3 bucket that is created for the cluster and CodeBuild project\. There are also roles for AWS Batch resources, instances, and jobs\.

For more information about IAM, see [https://aws\.amazon\.com/iam/](http://aws.amazon.com/iam/) and [https://docs\.aws\.amazon\.com/iam/](https://docs.aws.amazon.com/iam/)\.

## AWS Lambda<a name="aws-lambda"></a>

AWS Lambda \(Lambda\) runs the functions that orchestrate the creation of Docker images\. Lambda also manages the cleanup of custom cluster resources, such as Docker images stored in the Amazon ECR repository and on Amazon S3\.

Lambda is used only with AWS Batch clusters\.

For more information about Lambda, see [https://aws\.amazon\.com/lambda/](http://aws.amazon.com/lambda/) and [https://docs\.aws\.amazon\.com/lambda/](https://docs.aws.amazon.com/lambda/)\.

## NICE DCV<a name="nice-dcv"></a>

NICE DCV is a high\-performance remote display protocol that provides a secure way to deliver remote desktops and application streaming to any device over varying network conditions\. NICE DCV is used when the [`dcv_settings`](cluster-definition.md#dcv-settings) setting is specified and refers to an [`[dcv]` section](dcv-section.md)\. Support for NICE DCV was added in AWS ParallelCluster version 2\.5\.0\.

For more information about NICE DCV, see [https://aws\.amazon\.com/hpc/dcv/](http://aws.amazon.com/hpc/dcv/) and [https://docs\.aws\.amazon\.com/dcv/](https://docs.aws.amazon.com/dcv/)\.

## Amazon Route 53<a name="amazon-route-53"></a>

Amazon Route 53 \(Route 53\) is used to create hosted zones with hostnames and fully qualified domain names for each of the compute nodes\.

For more information about Route 53, see [https://aws\.amazon\.com/route53/](http://aws.amazon.com/route53/) and [https://docs\.aws\.amazon\.com/route53/](https://docs.aws.amazon.com/route53/)\.

## Amazon Simple Notification Service<a name="amazon-simple-notification-service-sns"></a>

Amazon Simple Notification Service \(Amazon SNS\) receives notifications from Auto Scaling\. These events are called lifecycle events and are generated when an instance launches or terminates in an Auto Scaling group\. Within AWS ParallelCluster, the Amazon SNS topic for the Auto Scaling group is subscribed to an Amazon SQS queue\.

Amazon SNS is not used with AWS Batch clusters\.

For more information about Amazon SNS, see [https://aws\.amazon\.com/sns/](http://aws.amazon.com/sns/) and [https://docs\.aws\.amazon\.com/sns/](https://docs.aws.amazon.com/sns/)\.

## Amazon Simple Queue Service<a name="amazon-simple-queuing-service-sqs"></a>

Amazon Simple Queue Service \(Amazon SQS\) holds notification sent from Auto Scaling, notifications sent through Amazon SNS, and notifications sent from the compute nodes\. Amazon SQS decouples the sending of notifications from the receiving of notifications\. This allows the head node to handle notifications through a polling process\. In this process, the head node runs Amazon SQSwatcher and polls the queue\. Auto Scaling and the compute nodes post messages to the queue\.

Amazon SQS is not used with AWS Batch clusters\.

For more information about Amazon SQS, see [https://aws\.amazon\.com/sqs/](http://aws.amazon.com/sqs/) and [https://docs\.aws\.amazon\.com/sqs/](https://docs.aws.amazon.com/sqs/)\.

## Amazon Simple Storage Service<a name="amazon-s3"></a>

Amazon Simple Storage Service \(Amazon S3\) stores AWS ParallelCluster templates located in each Region\. AWS ParallelCluster can be configured to allow CLI/SDK tools to use Amazon S3\.

When you use AWS Batch cluster, an Amazon S3 bucket in your account is used for storing related data\. For example, the bucket stores artifacts created when a Docker image and scripts are created from submitted jobs\.

For more information, see [https://aws\.amazon\.com/s3/](http://aws.amazon.com/s3/) and [https://docs\.aws\.amazon\.com/s3/](https://docs.aws.amazon.com/s3/)\.

## Amazon VPC<a name="amazon-vpc"></a>

Amazon VPC defines a network used by the nodes in your cluster\. The VPC settings for the cluster are defined in the [`[vpc]` section](vpc-section.md)\.

For more information about Amazon VPC, see [https://aws\.amazon\.com/vpc/](http://aws.amazon.com/vpc/) and [https://docs\.aws\.amazon\.com/vpc/](https://docs.aws.amazon.com/vpc/)\.