# `create_cluster`<a name="pc-py-lib-api-cluster-create"></a>

```
create_cluster(cluster_name, cluster_configuration, region, suppress_validators, validation_failure_level, dry_run, rollback_on_failure, wait)
```

Create a cluster in a given Region\.Parameters:

**`cluster_name` \(required\)**  
The cluster name\.

**`cluster_configuration` \(required\)**  
The cluster configuration as a Python data type\.

**`region`**  
The cluster AWS Region\.

**`suppress_validators`**  
Identifies one or more cluster configuration validators to suppress\.  
Format: `(ALL | type:[A-Za-z0-9]+)`

**`validation_failure_level`**  
The minimum validation level that causes the cluster creation to fail\. The default is `ERROR`\.  
Valid values: `INFO` \| `WARNING` \| `ERROR`\.

**`dry_run`**  
Performs the request validation without creating any resources\. You can use this to validate the cluster configuration\. The default is `False`\.

**`rollback_on_failure`**  
If set to `True`, AWS ParallelCluster automatically initiates a cluster stack rollback on failures\. The default is `True`\.

**`wait`**  
If set to `True`, AWS ParallelCluster waits for the operation to complete\. The default is `False`\.