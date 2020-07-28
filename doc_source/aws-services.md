# AWS services used in AWS ParallelCluster<a name="aws-services"></a>

The following Amazon Web Services \(AWS\) services are used in AWS ParallelCluster\.

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
+ [Amazon Simple Notification Service](#amazon-simple-notification-service-sns)
+ [Amazon Simple Queue Service](#amazon-simple-queuing-service-sqs)
+ [Amazon Simple Storage Service](#amazon-s3)
+ [Amazon VPC](#amazon-vpc)

## AWS Auto Scaling<a name="aws-auto-scaling"></a>

AWS Auto Scaling is used to manage the ComputeFleet instances\. These instances are managed as an AutoScaling Group, and can be elastically driven by workload, or can be static and driven by the configuration\.

AWS Auto Scaling is not used with AWS Batch clusters\.

For more details about AWS Auto Scaling, see [https://aws\.amazon\.com/autoscaling/](https://aws.amazon.com/autoscaling/)\.

## AWS Batch<a name="aws-batch"></a>

AWS Batch is the AWS\-managed job scheduler that dynamically provisions the optimal quantity and type of compute resources \(for example, CPU or memory\-optimized instances\)\. It provisions resources based on the volume and the requirements of the batch jobs that are submitted\. With AWS Batch, there is no need to install and manage batch computing software or server clusters to run your jobs\.

AWS Batch is used only with AWS Batch clusters\.

For more details, see [https://aws\.amazon\.com/batch/](https://aws.amazon.com/batch/)\.

## AWS CloudFormation<a name="aws-services-cloudformation"></a>

AWS CloudFormation is the core service used by AWS ParallelCluster\. Each cluster is represented as a stack\. All resources required by the cluster are defined within the AWS ParallelCluster AWS CloudFormation template\. AWS ParallelCluster CLI commands typically map to AWS CloudFormation stack commands, such as create, update, and delete\. Instances that are launched within a cluster make HTTPS calls to the AWS CloudFormation endpoint for the region in which the cluster is launched\.

For more details about AWS CloudFormation, see [https://aws\.amazon\.com/cloudformation/](https://aws.amazon.com/cloudformation/)\.

## Amazon CloudWatch<a name="amazon-cloudwatch"></a>

Amazon CloudWatch \(CloudWatch\) is used to log Docker image build steps and the standard output and error of the AWS Batch jobs\.

CloudWatch is used only with AWS Batch clusters\.

For more details, see [https://aws\.amazon\.com/cloudwatch/](https://aws.amazon.com/cloudwatch/)\.

## Amazon CloudWatch Logs<a name="amazon-cloudwatch-logs"></a>

Amazon CloudWatch Logs \(CloudWatch Logs\) is used to monitor, store, view, and search the log files for many components used by AWS ParallelCluster\. Prior to AWS ParallelCluster 2\.6\.0, CloudWatch Logs was only used with AWS Batch clusters\.

For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs.md)\.

## AWS CodeBuild<a name="aws-codebuild"></a>

AWS CodeBuild \(CodeBuild\) is used to automatically and transparently build Docker images at cluster creation time\.

CodeBuild is used only with AWS Batch clusters\.

For more details, see [https://aws\.amazon\.com/codebuild/](https://aws.amazon.com/codebuild/)\.

## Amazon DynamoDB<a name="amazon-dynamodb"></a>

Amazon DynamoDB \(DynamoDB\) is used to store minimal state of the cluster\. The MasterServer tracks provisioned instances in a DynamoDB table\.

DynamoDB is not used with AWS Batch clusters\.

For more details, see [https://aws\.amazon\.com/dynamodb/](https://aws.amazon.com/dynamodb/)\.

## Amazon Elastic Block Store<a name="amazon-elastic-block-store-ebs"></a>

Amazon Elastic Block Store \(Amazon EBS\) provides persistent storage for shared volumes\. All Amazon EBS settings can be passed through the configuration\. Amazon EBS volumes can either be initialized empty, or from an existing Amazon EBS snapshot\.

For more details about Amazon EBS, see [https://aws\.amazon\.com/ebs/](https://aws.amazon.com/ebs/)\.

## Amazon Elastic Compute Cloud<a name="amazon-ec2"></a>

Amazon Elastic Compute Cloud \(Amazon EC2\) provides the computing capacity for AWS ParallelCluster\. The MasterServer and ComputeFleet are Amazon EC2 instances\. Any instance type that support HVM can be selected\. The MasterServer and ComputeFleet can be different instance types, and the ComputeFleet can also be launched as a Spot instance\. Instance store volumes found on the instances are mounted as striped LVM volumes\.

For more details about Amazon EC2, see [https://aws\.amazon\.com/ec2/](https://aws.amazon.com/ec2/)\.

## Amazon Elastic Container Registry<a name="amazon-elastic-container-registry-ecr"></a>

Amazon Elastic Container Registry \(Amazon ECR\) stores the Docker images built at cluster creation time\. The Docker images are then used by AWS Batch to run the containers for the submitted jobs\.

Amazon ECR is used only with AWS Batch clusters\.

For more details, see [https://aws\.amazon\.com/ecr/](https://aws.amazon.com/ecr/)\.

## Amazon EFS<a name="amazon-efs"></a>

Amazon EFS provides a scalable NFS file system\. Amazon EFS is used when the [`efs_settings`](cluster-definition.md#efs-settings) setting is specified and refers to an [[efs] section](efs-section.md)\. Support for Amazon EFS was added in AWS ParallelCluster 2\.1\.0\.

For more details, see [https://aws\.amazon\.com/efs/](https://aws.amazon.com/efs/)\.

## Amazon FSx for Lustre<a name="amazon-fsx-for-lustre"></a>

Amazon FSx for Lustre provides a high\-performance file system using the open\-source Lustre file system\. Amazon FSx for Lustre is used when the [`fsx_settings`](cluster-definition.md#fsx-settings) setting is specified and refers to an [[fsx] section](fsx-section.md)\. Support for Amazon FSx for Lustre was added in AWS ParallelCluster 2\.2\.1\.

For more details, see [https://aws\.amazon\.com/fsx/lustre/](https://aws.amazon.com/fsx/lustre/)\.

## AWS Identity and Access Management<a name="aws-identity-and-access-management-iam"></a>

AWS Identity and Access Management \(IAM\) is used within AWS ParallelCluster\. It provides a least privileged IAM role for Amazon EC2 for the instance that is specific to each individual cluster\. AWS ParallelCluster instances are given access only to the specific API calls that are required to deploy and manage the cluster\.

With AWS Batch clusters, IAM roles are also created for the components that are involved with the Docker image building process at cluster creation time\. These components include the Lambda functions that are allowed to add and delete Docker images to and from the Amazon ECR repository, and to delete the Amazon S3 bucket that is created for the cluster and CodeBuild project\. There are also roles for AWS Batch resources, instances, and jobs\.

For more details about IAM, see [https://aws\.amazon\.com/iam/](https://aws.amazon.com/iam/)\.

## AWS Lambda<a name="aws-lambda"></a>

AWS Lambda \(Lambda\) runs the functions that orchestrate Docker image creation\. Lambda also manages the cleanup of custom cluster resources, such as Docker images stored in the Amazon ECR repository and on Amazon S3\.

Lambda is used only with AWS Batch clusters\.

For more details, see [https://aws\.amazon\.com/lambda/](https://aws.amazon.com/lambda/)\.

## NICE DCV<a name="nice-dcv"></a>

NICE DCV is a high\-performance remote display protocol that provides a secure way to deliver remote desktops and application streaming to any device, over varying network conditions\. NICE DCV is used when the [`dcv_settings`](cluster-definition.md#dcv-settings) setting is specified and refers to an [[dcv] section](dcv-section.md)\. Support for NICE DCV was added in AWS ParallelCluster 2\.5\.0\.

For more details, see [https://aws\.amazon\.com/hpc/dcv/](https://aws.amazon.com/hpc/dcv/)\.

## Amazon Simple Notification Service<a name="amazon-simple-notification-service-sns"></a>

Amazon Simple Notification Service \(Amazon SNS\) is used to receive notifications from Auto Scaling\. These events are called life cycle events, and are generated when an instance launches or terminates in an Autoscaling Group\. Within AWS ParallelCluster, the Amazon SNS topic for the Autoscaling Group is subscribed to an Amazon SQS queue\.

Amazon SNS is not used with AWS Batch clusters\.

For more details about Amazon SNS, see [https://aws\.amazon\.com/sns/](https://aws.amazon.com/sns/)\.

## Amazon Simple Queue Service<a name="amazon-simple-queuing-service-sqs"></a>

Amazon Simple Queue Service \(Amazon SQS\) is used to hold notification messages from Auto Scaling, sent through Amazon SNS, and notifications from the ComputeFleet instances\. Using Amazon SQS decouples the sending of notifications from receiving them, and allows the Master to handle them through polling\. The MasterServer runs Amazon SQSwatcher and polls the queue\. Auto Scaling and the ComputeFleet instances post messages to the queue\.

Amazon SQS is not used with AWS Batch clusters\.

For more details about Amazon SQS, see [https://aws\.amazon\.com/sqs/](https://aws.amazon.com/sqs/)\.

## Amazon Simple Storage Service<a name="amazon-s3"></a>

Amazon Simple Storage Service \(Amazon S3\) is used to store the AWS ParallelCluster templates used in each region\. AWS ParallelCluster can be configured to allow CLI/SDK tools to use Amazon S3\.

When an AWS Batch cluster is used, an Amazon S3 bucket in the customer's account is used for storage\. For example, it stores artifacts used by the Docker image creation, and scripts from submitted jobs\.

For more details, see [https://aws\.amazon\.com/s3/](https://aws.amazon.com/s3/)\.

## Amazon VPC<a name="amazon-vpc"></a>

Amazon VPC is used to define a network used by the nodes in your cluster\. The VPC settings for the cluster are defined in the [[vpc] section](vpc-section.md)\.

For more details, see [https://aws\.amazon\.com/vpc/](https://aws.amazon.com/vpc/)\.