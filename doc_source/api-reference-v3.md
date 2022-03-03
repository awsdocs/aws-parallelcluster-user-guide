# AWS ParallelCluster API<a name="api-reference-v3"></a>

 **What is AWS ParallelCluster API?** 

 AWS ParallelCluster API is a serverless application that once deployed to your AWS account will enable programmatic access to AWS ParallelCluster features via API\. 

 AWS ParallelCluster API is distributed as a self\-contained AWS CloudFormation template mainly consisting of an Amazon API Gateway endpoint, that exposes AWS ParallelCluster features, and an AWS Lambda function, that takes care of executing the invoked features\. 

 The image below shows a high level architecture diagram of the AWS ParallelCluster API infrastructure\.  

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/API-Architecture.png) 

 **AWS ParallelCluster API Documentation** 

 The OpenAPI specification file describing the AWS ParallelCluster API can be downloaded from: 

```
https://<REGION>-aws-parallelcluster.s3.<REGION>.amazonaws.com/parallelcluster/<VERSION>/api/ParallelCluster.openapi.yaml
```

Starting from the OpenAPI specification file, you can generate documentation for the AWS ParallelCluster API by using one of the many available tools such as [Swagger UI](https://swagger.io/tools/swagger-ui/) or [Redoc](https://github.com/Redocly/redoc)\. 

 **How to deploy AWS ParallelCluster API** 

 To deploy AWS ParallelCluster API you need to be an Administrator of the AWS account\. 

 The template used to deploy the API is available at the following URL:

```
https://<REGION>-aws-parallelcluster.s3.<REGION>.amazonaws.com/parallelcluster/<VERSION>/api/parallelcluster-api.yaml
```

 where `<REGION>` is the AWS Region where the API needs to be deployed to and `<VERSION>` is the AWS ParallelCluster version \(e\.g\. 3\.1\.2\)\. 

 The Docker image used to deploy the AWS Lambda function implementing AWS ParallelCluster features is available at:  [https://gallery\.ecr\.aws/parallelcluster/pcluster\-api](https://gallery.ecr.aws/parallelcluster/pcluster-api) 

**Warning**  
Any user in the AWS account, that has privileged access to AWS Lambda or Amazon API Gateway services, will automatically inherit permissions to administer AWS ParallelCluster API resources\. 

 **Deploy with AWS CLI** 

Configure AWS Credentials to be used with the CLI if not done already\.

```
aws configure
```

Run the following command to deploy the API

```
REGION=<region>
API_STACK_NAME=<stack-name>  # This can be any name
VERSION=3.1.2
aws cloudformation create-stack \
    --region ${REGION} \
    --stack-name ${API_STACK_NAME} \
    --template-url https://${REGION}-aws-parallelcluster.s3.${REGION}.amazonaws.com/parallelcluster/${VERSION}/api/parallelcluster-api.yaml \
    --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
aws cloudformation wait stack-create-complete --stack-name ${API_STACK_NAME} --region ${REGION}
```

 **Customize your deployment** 

 The API deployment can be customized by using the AWS CloudFormation parameters exposed by the template\. To configure the value of a parameter when deploying through the CLI the following option can be used: `--parameters ParameterKey=KeyName,ParameterValue=Value`\. 

 The parameters documented below are all optional\. 
+ **Region** \- The `Region` parameter can be used to determine whether the API is able to control resources in all AWS Regions \(default\) or in a single AWS Region\. Please set this value to the Region the API is being deployed to in order to restrict access\.
+ **ParallelClusterFunctionRole** \- This overrides the IAM role that gets assigned to the AWS Lambda function implementing AWS ParallelCluster features\. The parameter accepts the ARN of a IAM role\. Such role needs to be configured to have AWS Lambda as the IAM principal\. 
+  **CustomDomainName, CustomDomainCertificate, CustomDomainHostedZoneId** \- These parameters allow to set a custom domain for the Amazon API Gateway endpoint\. `CustomDomainName` is the name of the domain to use, `CustomDomainCertificate` is the ARN of an AWS managed certificate for this domain name and `CustomDomainHostedZoneId` is the id of the Route 53 hosted zone that you want to create records in\. 
+  **EnableIamAdminAccess** \- By default the AWS Lambda function executing AWS ParallelCluster API operations is configured with a IAM role that prevents any privileged IAM access \(`EnableIamAdminAccess=false`\)\. This makes the API unable to execute operations that require the creation of IAM roles or policies\. Because of this, the creation of clusters or custom images is successful only when IAM roles are provided as input as part of the resource configuration\. 

   When `EnableIamAdminAccess` is set to `true` the AWS ParallelCluster API is granted permissions to manage the creation of IAM roles required to deploy clusters or generating custom AMIs\. 
**Warning**  
 Setting this to true grants IAM admin privileges to the AWS Lambda function executing AWS ParallelCluster operations\. 

   Please refer to [User Policy to manage IAM resources](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-user-policy-manage-iam) for additional details on the features that can be unlocked when enabling this mode\.
+  **PermissionsBoundaryPolicy** \- This parameter accepts the ARN of a IAM policy and can be used only when `EnableIamAdminAccess` is set to `true`\. When a IAM policy is specified, the IAM permissions granted to the API AWS Lambda function is conditionally restricted to the usage of the given permissions boundary\. 

  Please refer to [`PermissionsBoundary` mode](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-permissionsboundary-mode) for additional details on the restrictions imposed by this mode\.
+  **CreateApiUserRole** \- By default the deployment of the AWS ParallelCluster API includes the creation of a IAM role which is set as the only role authorized to invoke the API\. The Amazon API Gateway endpoint is in fact configured with a resource based policy to grant invoke permission to the created user only\. To lift such restriction set `CreateApiUserRole=false` and then grant access to the API to selected IAM users\. For more information, see [Control access for invoking an API](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-control-access-using-iam-policies-to-invoke-api.html) in the *Amazon API Gateway Developer Guide*\.
**Warning**  
 When `CreateApiUserRole=true` access to the API endpoint is not restricted by Amazon API Gateway resource policies,  all IAM roles having unconstrained `execute-api:Invoke` permission will be able to access AWS ParallelCluster features\. For more information, see \.[Controlling access to an API with API Gateway resource policies](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-resource-policies.html) in the *API Gateway Developer Guide*\.
**Warning**  
 The `ParallelClusterApiUserRole` has invoke permissions for all AWS ParallelCluster API operations\. To restrict access to a subset of API resources please see the [Control who can call an API Gateway API method with IAM policies](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-control-access-using-iam-policies-to-invoke-api.html#api-gateway-who-can-invoke-an-api-method-using-iam-policies) in the *API Gateway Developer Guide*\.

 **Updating the API** 

 **Use case 1: upgrading to a newer AWS ParallelCluster version** 

 Option 1: Remove the existing API by deleting the corresponding AWS CloudFormation stack and deploy the new API as shown above\.

 Option 2: Update the existing API by running the following instructions: 

```
REGION=<region>
API_STACK_NAME=<stack-name>  # This needs to correspond to the existing API stack name
VERSION=3.1.2
aws cloudformation update-stack \
    --region ${REGION} \
    --stack-name ${API_STACK_NAME} \  
    --template-url https://${REGION}-aws-parallelcluster.s3.${REGION}.amazonaws.com/parallelcluster/${VERSION}/api/parallelcluster-api.yaml \
    --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
aws cloudformation wait stack-update-complete --stack-name ${API_STACK_NAME} --region ${REGION}
```

 **Use case 2: updating the API to the latest available Docker image build for the deployed AWS ParallelCluster version** 

AWS ParallelCluster can periodically deploy refreshed Docker images for the API AWS Lambda function that contain updated and patched dependencies\. Such refreshed images do not include any change in terms of the features shipped with the given AWS ParallelCluster version\. In order to use the latest Docker image for the deployed version of the API you can run the following instructions:

```
REGION=<region>
API_STACK_NAME=<stack-name>
DOCKER_UPDATE_IMAGE_PIPELINE=$(aws cloudformation describe-stacks --region ${REGION} --stack-name ${API_STACK_NAME} --query "Stacks[0].Outputs[?OutputKey=='ParallelClusterDockerUpdateImagePipeline'].OutputValue" --output text)
aws imagebuilder start-image-pipeline-execution --region ${REGION} --image-pipeline-arn ${DOCKER_UPDATE_IMAGE_PIPELINE}
```

 This triggers a build of an EC2 Image Builder pipeline that takes care of fetching the latest available AWS ParallelCluster Docker image for the deployed API version\. 

 **Invoking AWS ParallelCluster API** 

 The AWS ParallelCluster Amazon API Gateway endpoint is configured with [`AWS_IAM` authorization type](https://docs.aws.amazon.com/apigateway/latest/developerguide/permissions.html#api-gateway-control-access-iam-permissions-model-for-calling-api), thus requiring all request to be SigV4 signed with valid IAM credentials \([API reference: making http requests](https://docs.aws.amazon.com/apigateway/api-reference/making-http-requests/)\)\. 

 When deployed with default settings, API invoke permissions are only granted to the default IAM user created with the API\. 

 To retrieve the ARN of such user run: 

```
REGION=<region>
API_STACK_NAME=<stack-name>
aws cloudformation describe-stacks --region ${REGION} --stack-name ${API_STACK_NAME} --query "Stacks[0].Outputs[?OutputKey=='ParallelClusterApiUserRole'].OutputValue" --output text
```

 Temporary credentials for such user can be obtained with a call to [STS AssumeRole](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sts/assume-role.html)\. 

 The AWS ParallelCluster API endpoint can be obtained by running the following command: 

```
REGION=<region>
API_STACK_NAME=<stack-name>
aws cloudformation describe-stacks --region ${REGION} --stack-name ${API_STACK_NAME} --query "Stacks[0].Outputs[?OutputKey=='ParallelClusterApiInvokeUrl'].OutputValue" --output text
```

 The AWS ParallelCluster API can be invoked by any HTTP client that complies with the OpenAPI specifications that can be found here: 

 `https://<REGION>-aws-parallelcluster.s3.<REGION>.amazonaws.com/parallelcluster/<VERSION>/api/ParallelCluster.openapi.yaml` 

 Requests need to be SigV4 signed as documented [here](https://docs.aws.amazon.com/apigateway/api-reference/signing-requests)\. 

 At this time we do not offer any official API client implementation\. However API clients can be easily generated from the OpenAPI model by using [OpenAPI Generator](https://openapi-generator.tech/)\. Once the client is generated SigV4 signing needs to be added if not provided out of the box\. 

 A reference implementation for a Python API client can be found in the [AWS ParallelCluster repository](https://github.com/aws/aws-parallelcluster/tree/develop/api/client/src)\.

 To implement more advanced access control mechanism, such as Amazon Cognito or Lambda Authorizers, or to further protect the API with AWS WAF or API keys please follow the [Amazon API Gateway documentation](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html)\. 

**Warning**  
 An IAM user authorized to invoke the AWS ParallelCluster API will be able to indirectly control all AWS resources managed by AWS ParallelCluster in the AWS account\. This includes the creation of AWS resources that the IAM user cannot control directly due to restrictions on his IAM policy\. For example the creation of a AWS ParallelCluster cluster, depending on its configuration, may include the deployment of Amazon EC2 instances, Amazon Route 53, Amazon Elastic File System filesystems, Amazon FSx filesystems, IAM roles, and resources from other AWS services used by AWS ParallelCluster that the IAM user might not have direct control over\. 

 **Accessing the API logs and metrics** 

 API logs are published to Amazon CloudWatch with a retention of 30 days\. To retrieve the LogGroup name associated with an API deployment run the following command: 

```
REGION=<region>
API_STACK_NAME=<stack-name>
aws cloudformation describe-stacks --region ${REGION} --stack-name ${API_STACK_NAME} --query "Stacks[0].Outputs[?OutputKey=='ParallelClusterLambdaLogGroup'].OutputValue" --output text
```

Lambda metrics, logs and AWS X\-Ray trace logs can be also accessed through the Lambda console\. To retrieve the ARN of the Lambda function associated with an API deployment run the following command: 

```
REGION=<region>
API_STACK_NAME=<stack-name>
aws cloudformation describe-stacks --region ${REGION} --stack-name ${API_STACK_NAME} --query "Stacks[0].Outputs[?OutputKey=='ParallelClusterLambdaArn'].OutputValue" --output text
```