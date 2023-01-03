# `pcluster describe-compute-fleet`<a name="pcluster.describe-compute-fleet-v3"></a>

Describe the status of the compute fleet\.

```
pcluster describe-compute-fleet [-h] 
                 --cluster-name CLUSTER_NAME
                [--debug]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.describe-compute-fleet.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster describe-compute-fleet`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster describe-compute-fleet -n pcluster-v3
{
  "status": "RUNNING",
  "lastStatusUpdatedTime": "2022-07-12T17:24:26.000Z"
}
```