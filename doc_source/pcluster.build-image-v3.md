# `pcluster build-image`<a name="pcluster.build-image-v3"></a>

Create a custom AWS ParallelCluster image in the specified Region\.

```
pcluster build-image [-h]
                     [--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]]
                     [--validation-failure-level {INFO,WARNING,ERROR}]
                     [--dryrun DRYRUN]
                     [--rollback-on-failure ROLLBACK_ON_FAILURE]
                     [--region REGION]
                     --image-configuration IMAGE_CONFIGURATION --image-id IMAGE_ID
                     [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.build-image.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster build-image`\.

`--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]`  
Identifies one or more config validators to suppress\.  
Format: \(`ALL`\|`type:[A-Za-z0-9]+`\)

`--validation-failure-level {INFO,WARNING,ERROR}`  
Specifies the minimum validation level that will cause the creation to fail\. \(Defaults to `ERROR`\.\)

`--dryrun DRYRUN`  
Only perform the requested validation without creating any resources\. It can be used to validate the image configuration\. \(Defaults to `false`\.\)

`--rollback-on-failure ROLLBACK_ON_FAILURE`  
When set, will automatically initiate an image stack rollback on failure\. \(Defaults to `false`\.\)

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the [Region](image-builder-configuration-file-v3.md#yaml-build-image-Region) setting in the image configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--image-configuration IMAGE_CONFIGURATION`  
Specifies the image configuration file as a YAML document\.

`--image-id IMAGE_ID`  
Specifies the id of the image that will be built\.

`--debug`  
Turn on debug logging\.

`--query QUERY`  
JMESPath query to perform on output\.

**Warning**  
`pcluster build-image` uses the default VPC\. If the default VPC has been deleted, perhaps by using AWS Control Tower or AWS Landing Zone, then the subnet ID must be specified in the image configuration file\. For more information, see [SubnetId](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId)\.