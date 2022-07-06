# Using the AWS ParallelCluster API<a name="tutorials_06_API_use"></a>

In this tutorial, you build and test the API with [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html) and a AWS ParallelCluster CloudFormation template\. Then you use the example client available on GitHub to use the API\. For more information on using the API, see the [AWS ParallelCluster API](api-reference-v3.md)\.

This tutorial was excerpted from the [HPC For Public Sector Customers Workshop](https://catalog.prod.workshops.aws/workshops/e2f40d13-8082-4718-909b-6cdc3155ae41/en-US/examples/pcluster-api)\.

**Prerequisites**
+ The AWS CLI is [installed](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and configured in your compute environment\.
+ AWS ParallelCluster is installed in a virtual environment\. For more information, see [Install AWS ParallelCluster in a virtual environment](https://docs.aws.amazon.com/parallelcluster/latest/ug/install-v3-virtual-environment.html)\.
+ You have an [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ You have an IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) required to run the [`pcluster`](pcluster-v3.md) CLI\.

