# `pcluster delete-cluster`<a name="pcluster.delete-cluster-v3"></a>

Starts the deletion of a cluster\.

```
pcluster delete-cluster [-h] --cluster-name CLUSTER_NAME
                        [--region REGION] [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.delete-cluster.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster delete-cluster`\.

`--cluster-name CLUSTER_NAME`  
Specifies the name of the cluster\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.