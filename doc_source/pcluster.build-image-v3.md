# `pcluster build-image`<a name="pcluster.build-image-v3"></a>

Create a custom AWS ParallelCluster image in the specified Region\.

```
pcluster build-image [-h]
                 --image-configuration IMAGE_CONFIGURATION 
                 --image-id IMAGE_ID
                [--debug]
                [--dryrun DRYRUN]
                [--query QUERY]
                [--region REGION]
                [--rollback-on-failure ROLLBACK_ON_FAILURE]
                [--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]]
                [--validation-failure-level {INFO,WARNING,ERROR}]
```

## Named arguments<a name="pcluster-v3.build-image.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster build-image`\.

`--image-configuration, -c IMAGE_CONFIGURATION`  
Specifies the image configuration file as a YAML document\.

`--image-id, -i IMAGE_ID`  
Specifies the id of the image that will be built\.

`--debug`  
Turn on debug logging\.

`--dryrun DRYRUN`  
When `true`, the command performs validation without creating any resources\. You can use this to validate the image configuration\. \(Defaults to `false`\.\)

`--query QUERY`  
JMESPath query to perform on output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the [Region](image-builder-configuration-file-v3.md#yaml-build-image-Region) setting in the image configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--rollback-on-failure ROLLBACK_ON_FAILURE`  
When `true`, automatically initiates an image stack rollback on failure\. \(Defaults to `false`\.\)

`--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]`  
Identifies one or more config validators to suppress\.  
Format: \(`ALL`\|`type:[A-Za-z0-9]+`\)

`--validation-failure-level {INFO,WARNING,ERROR}`  
Specifies the minimum validation level that will cause the creation to fail\. \(Defaults to `ERROR`\.\)

**Example using AWS ParallelCluster version 3\.1\.2:**

```
$ pcluster build-image --image-configuration image-config.yaml --image-id custom-alinux2-image
{
  "image": {
    "imageId": "custom-alinux2-image",
    "imageBuildStatus": "BUILD_IN_PROGRESS",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:us-east-1:123456789012:stack/custom-alinux2-image/1234abcd-56ef-78gh-90ij-abcd1234efgh",
    "region": "us-east-1",
    "version": "3.1.2"
  }
}
```

**Warning**  
`pcluster build-image` uses the default VPC\. If the default VPC has been deleted, perhaps by using AWS Control Tower or AWS Landing Zone, then the subnet ID must be specified in the image configuration file\. For more information, see [SubnetId](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId)\.