# `pcluster list-images`<a name="pcluster.list-images-v3"></a>

Retrieve the list of existing custom images\.

```
pcluster list-images [-h] [--region REGION] [--next-token NEXT_TOKEN]
                     --image-status {AVAILABLE,PENDING,FAILED}
                     [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.list-images.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-images`\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--image-status {AVAILABLE,PENDING,FAILED}`  
Filter images by the status provided\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.