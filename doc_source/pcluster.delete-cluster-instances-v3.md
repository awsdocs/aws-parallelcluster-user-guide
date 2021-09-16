# `pcluster delete-cluster-instances`<a name="pcluster.delete-cluster-instances-v3"></a>

Initiate the forced termination of all cluster compute nodes\. This does not work with AWS Batch clusters\.

```
pcluster delete-cluster-instances [-h] --cluster-name CLUSTER_NAME
                                  [--region REGION] [--force FORCE]
                                  [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.delete-cluster-instances.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster delete-cluster-instances`\.

`--cluster-name CLUSTER_NAME`  
Specifies the name of the cluster\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, either using the `AWS_DEFAULT_REGION` environment variable or the `--region` parameter\.

`--force FORCE`  
Forces the update by ignoring the update validation errors\. \(Defaults to `false`\.\)

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.