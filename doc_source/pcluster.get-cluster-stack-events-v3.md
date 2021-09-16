# `pcluster get-cluster-stack-events`<a name="pcluster.get-cluster-stack-events-v3"></a>

Retrieve the events associated with the stack for the specified cluster\.

```
pcluster get-cluster-stack-events [-h] --cluster-name CLUSTER_NAME
                                  [--region REGION]
                                  [--next-token NEXT_TOKEN] [--debug]
                                  [--query QUERY]
```

## Named arguments<a name="pcluster-v3.get-cluster-stack-events.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster get-cluster-stack-events`\.

`--cluster-name CLUSTER_NAME`  
Specifies the name of the cluster\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, either using the `AWS_DEFAULT_REGION` environment variable or the `--region` parameter\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.