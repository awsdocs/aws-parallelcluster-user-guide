# `pcluster update-compute-fleet`<a name="pcluster.update-compute-fleet-v3"></a>

Updates the status of the cluster compute fleet\.

```
pcluster update-compute-fleet [-h] 
                 --cluster-name CLUSTER_NAME
                 --status {START_REQUESTED,STOP_REQUESTED,ENABLED,DISABLED}                             
                [--debug]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.update-compute-fleet.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster update-compute-fleet`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--status {START_REQUESTED,STOP_REQUESTED,ENABLED,DISABLED}`  
Specifies the status applied to the cluster compute fleet\. The statuses `START_REQUESTED` and `STOP_REQUESTED` correspond to the slurm scheduler while the statuses `ENABLED` and `DISABLED` correspond to the AWS Batch scheduler\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster update-compute-fleet -n cluster-v3 --status STOP_REQUESTED
{
  "status": "STOP_REQUESTED",
  "lastStatusUpdatedTime": "2022-07-12T20:19:47.653Z"
}
```