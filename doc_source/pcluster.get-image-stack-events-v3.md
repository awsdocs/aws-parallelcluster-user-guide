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