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
Only perform the requested validation without creating any resources\. It can be used to validate the image configuration\. \(Defaults to `false`\.\)

`--query QUERY`  
JMESPath query to perform on output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the [Region](image-builder-configuration-file-v3.md#yaml-build-image-Region) setting in the image configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--rollback-on-failure ROLLBACK_ON_FAILURE`  
When set, will automatically initiate an image stack rollback on failure\. \(Defaults to `false`\.\)

`--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]`  
Identifies one or more config validators to suppress\.  
Format: \(`ALL`\|`type:[A-Za-z0-9]+`\)

`--validation-failure-level {INFO,WARNING,ERROR}`  
Specifies the minimum validation level that will cause the creation to fail\. \(Defaults to `ERROR`\.\)

**Warning**  
`pcluster build-image` uses the default VPC\. If the default VPC has been deleted, perhaps by using AWS Control Tower or AWS Landing Zone, then the subnet ID must be specified in the image configuration file\. For more information, see [SubnetId](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId)\.