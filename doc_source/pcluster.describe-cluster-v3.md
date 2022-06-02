# `pcluster describe-cluster`<a name="pcluster.describe-cluster-v3"></a>

Get detailed information about a cluster\.

```
pcluster describe-cluster [-h] 
                 --cluster-name CLUSTER_NAME
                [--debug]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.describe-cluster.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster describe-cluster`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.