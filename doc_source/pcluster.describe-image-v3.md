# `pcluster describe-image`<a name="pcluster.describe-image-v3"></a>

Get detailed information about an image\.

```
pcluster describe-image [-h] 
                 --image-id IMAGE_ID
                [--debug] 
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.describe-image.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster describe-image`\.

`--image-id, -i IMAGE_ID`  
Specifies the ID of the image\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.