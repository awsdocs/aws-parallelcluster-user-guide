# AWS Lambda VPC configuration in AWS ParallelCluster<a name="lambda-vpc-v3"></a>

AWS ParallelCluster uses AWS Lambda to perform operations during the lifecycle of the cluster\. An [AWS Lambda function always runs in a VPC](https://docs.aws.amazon.com/lambda/latest/dg/foundation-networking.html) owned by the Lambda service\. This Lambda function can also be connected to private subnets in a virtual private cloud \(VPC\) to access private resources\.

**Note**  
Lambda functions can't connect directly to a VPC with dedicated instance tenancy\. To connect to resources in a dedicated VPC, peer the dedicated VPC to a second VPC with a default tenancy that can connect to a dedicated VPC\.  
For more information, see [Dedicated Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *EC2 User Guide for Linux Instances* and [How do I connect a Lambda function to a dedicated VPC?](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-dedicated-vpc/) from the *AWS Knowledge Center*\.

Lambda functions that are created by AWS ParallelCluster can be connected to a private VPC\. These Lambda functions need to access AWS services\. You can provide access through the internet or VPC endpoints by using the following methods\.
+ **Internet access**

  To access the internet and AWS services, a Lambda function requires network address translation \(NAT\)\. Route outbound traffic from your private subnet to a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in a public subnet\.
+ **VPC endpoints**

  Several AWS services offer [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\. You can use VPC endpoints to connect to AWS services from a VPC that doesn't have internet access\. To view the list of AWS ParallelCluster VPC endpoints, see [Networking](https://docs.aws.amazon.com/parallelcluster/latest/ug/network-configuration-v3.html#aws-parallelcluster-in-a-single-public-subnet-no-internet-v3)\.

**Note**  
Every combination of subnets and security groups must provide access to AWS services using one these methods\. Subnets and security groups must be in the same VPC\.

 For more information, see [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html) in the *Amazon Virtual Private Cloud User Guide* and [Internet and service access for VPC\-connected functions](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-internet) in the *AWS Lambda Developer Guide*\.

To configure the use of Lambda functions and VPCs, see [`DeploymentSettings`](DeploymentSettings-cluster-v3.md) / [`LambdaFunctionsVpcConfig`](DeploymentSettings-cluster-v3.md#DeploymentSettings-cluster-v3-LambdaFunctionsVpcConfig) for clusters or [`DeploymentSettings`](DeploymentSettings-build-image-v3.md) / [`LambdaFunctionsVpcConfig`](DeploymentSettings-build-image-v3.md#DeploymentSettings-build-image-v3-LambdaFunctionsVpcConfig) for images\. 