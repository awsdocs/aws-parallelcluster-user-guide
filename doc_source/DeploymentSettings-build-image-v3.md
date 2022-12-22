# `DeploymentSettings` section<a name="DeploymentSettings-build-image-v3"></a>

**\(Optional\)** Specifies the deployment settings configuration\.

```
DeploymentSettings:
  LambdaFunctionsVpcConfig:
    SecurityGroupIds
      - string
    SubnetIds
      - string
```

## `DeploymentSettings` properties<a name="DeploymentSettings-build-image-v3.properties"></a>

### `LambdaFunctionsVpcConfig`<a name="DeploymentSettings-build-image-v3-LambdaFunctionsVpcConfig"></a>

**\(Optional\)** Specifies the AWS Lambda functions VPC configurations\. For more information, see [AWS Lambda VPC configuration in AWS ParallelCluster](lambda-vpc-v3.md)\.

```
LambdaFunctionsVpcConfig:
  SecurityGroupIds
    - string
  SubnetIds
    - string
```

#### `LambdaFunctionsVpcConfig properties`<a name="DeploymentSettings-build-image-v3-LambdaFunctionsVpcConfig.properties"></a>

`SecurityGroupIds` \(**Required**, `[String]`\)  
The list of Amazon VPC security group IDs that are attached to the Lambda functions\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`SubnetIds` \(**Required**, `[String]`\)  
The list of subnet IDs that are attached to the Lambda functions\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

**Note**  
The subnets and security groups must be in the same VPC\.

**Note**  
`DeploymentSettings` is added starting with AWS ParallelCluster version 3\.4\.0\.