# `pcluster delete-image`<a name="pcluster.delete-image-v3"></a>

Starts the deletion of the custom AWS ParallelCluster image\.

```
pcluster delete-image [-h] 
                 --image-id IMAGE_ID
                [--debug]
                [--force FORCE]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.delete-image.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster delete-image`\.

`--image-id, -i IMAGE_ID`  
Specifies the id of the image that will be deleted\.

`--debug`  
Enables debug logging\.

`--force FORCE`  
When `true`, forces the deletion in case there are instances using the AMI or if the AMI is shared\. \(Defaults to `false`\.\)

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster delete-image --image-id custom-alinux2-image
{
  "image": {
    "imageId": "custom-alinux2-image",
    "imageBuildStatus": "DELETE_IN_PROGRESS",
    "region": "us-east-1",
    "version": "3.1.4"
  }
}
```