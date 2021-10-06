# `pcluster list-cluster-log-streams`<a name="pcluster.list-cluster-log-streams-v3"></a>

Retrieve the list of log streams associated with a cluster\.

```
pcluster list-cluster-log-streams [-h] --cluster-name CLUSTER_NAME
                                  [--region REGION]
                                  [--filters FILTERS [FILTERS ...]]
                                  [--next-token NEXT_TOKEN] [--debug]
                                  [--query QUERY]
```

## Named arguments<a name="pcluster-v3.list-cluster-log-streams.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-cluster-log-streams`\.

`--cluster-name CLUSTER_NAME`  
Specifies the name of the cluster\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, either using the `AWS_DEFAULT_REGION` environment variable or the `--region` parameter\.

`--filters FILTERS [FILTERS ...]`  
Specifies filters for the log streams\. Format: `Name=a,Values=1 Name=b,Values=2,3`\. Supported filters are:    
`private-dns-name`  
Specifies the short form of the private DNS name of the instance \(e\.g\. `ip-10-0-0-101`\)\.  
`node-type`  
Specifies the node type, the only accepted value for this filter is `HeadNode`\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.