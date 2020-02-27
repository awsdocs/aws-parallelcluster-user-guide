# `pcluster delete`<a name="pcluster.delete"></a>

Deletes a cluster\.

```
pcluster delete [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] [ -nw ] cluster_name
```

## Positional Arguments<a name="pcluster.delete.arg"></a>

`cluster_name`  
Specifies the name of the cluster to delete\.

## Named Arguments<a name="pcluster.delete.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`--keep-logs`  
Keep the CloudWatch Logs data after deleting the cluster\. The log group remains until it's deleted manually, but the log events will expire based on the [`retention_days`](cw-log-section.md#cw-log-section-retention-days) setting\. The setting defaults to 14 days\.  
Support for the `--keep-logs` argument was added in AWS ParallelCluster 2\.6\.0\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

When the command is called and begins polling for the status of that call, it is safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.