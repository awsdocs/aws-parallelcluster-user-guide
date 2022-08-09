# `pcluster list-image-log-streams`<a name="pcluster.list-image-log-streams-v3"></a>

Retrieve the list of log streams associated with an image\.

```
pcluster list-image-log-streams [-h] 
                 --image-id IMAGE_ID
                [--next-token NEXT_TOKEN] [--debug]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.list-image-log-streams.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-image-log-streams`\.

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
$ pcluster list-image-log-streams --image-id custom-alinux2-image --region us-east-1 --query 'logStreams[*].logStreamName'
[
  "3.0.0/1",
  "3.1.2/1"
]
```