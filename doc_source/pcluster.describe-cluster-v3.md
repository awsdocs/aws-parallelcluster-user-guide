# `pcluster describe-cluster`<a name="pcluster.describe-cluster-v3"></a>

Get detailed information about a cluster\.

```
pcluster describe-cluster [-h] --cluster-name CLUSTER_NAME
                          [--region REGION] [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.describe-cluster.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster describe-cluster`\.

`--cluster-name CLUSTER_NAME`  
Specifies the name of the cluster\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, either using the `AWS_DEFAULT_REGION` environment variable or the `--region` parameter\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.