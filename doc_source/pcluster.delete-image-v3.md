# `pcluster delete-image`<a name="pcluster.delete-image-v3"></a>

Starts the deletion of the custom AWS ParallelCluster image\.

```
pcluster delete-image [-h] --image-id IMAGE_ID [--region REGION]
                      [--force FORCE] [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.delete-image.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster delete-image`\.

`--image-id, -i IMAGE_ID`
Specifies the id of the image that will be deleted\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--force FORCE`  
Forces the deletion in case there are instances using the AMI or if the AMI is shared\. \(Defaults to `false`\.\)

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.
