# `pcluster get-image-stack-events`<a name="pcluster.get-image-stack-events-v3"></a>

Retrieve the events associated with the stack for the specified image build\.

```
pcluster get-image-stack-events [-h] 
                 --image-id IMAGE_ID
                [--debug]
                [--next-token NEXT_TOKEN]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.get-image-stack-events.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster get-image-stack-events`\.

`--image-id, -i IMAGE_ID`  
Specifies the ID of the image\.

`--debug`  
Enables debug logging\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

**Example using AWS ParallelCluster version 3\.1\.2:**

```
$ pcluster get-image-stack-events --image-id custom-alinux2-image --region us-east-1 --query "events[0]"
   {
  "eventId": "ParallelClusterImage-CREATE_IN_PROGRESS-2022-04-05T21:39:24.725Z",
  "physicalResourceId": "arn:aws:imagebuilder:us-east-1:123456789012:image/parallelclusterimage-custom-alinux2-image/3.1.2/1",
  "resourceStatus": "CREATE_IN_PROGRESS",
  "resourceStatusReason": "Resource creation Initiated",
  "resourceProperties": "{\"InfrastructureConfigurationArn\":\"arn:aws:imagebuilder:us-east-1:123456789012:infrastructure-configuration/parallelclusterimage-1234abcd-56ef-78gh-90ij-abcd1234efgh\",\"ImageRecipeArn\":\"arn:aws:imagebuilder:us-east-1:123456789012:image-recipe/parallelclusterimage-custom-alinux2-image/3.1.2\",\"DistributionConfigurationArn\":\"arn:aws:imagebuilder:us-east-1:123456789012:distribution-configuration/parallelclusterimage-1234abcd-56ef-78gh-90ij-abcd1234efgh\",\"EnhancedImageMetadataEnabled\":\"false\",\"Tags\":{\"parallelcluster:image_name\":\"custom-alinux2-image\",\"parallelcluster:image_id\":\"custom-alinux2-image\"}}",
  "stackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/custom-alinux2-image/1234abcd-56ef-78gh-90ij-abcd1234efgh",
  "stackName": "custom-alinux2-image",
  "logicalResourceId": "ParallelClusterImage",
  "resourceType": "AWS::ImageBuilder::Image",
  "timestamp": "2022-04-05T21:39:24.725Z"
}
```