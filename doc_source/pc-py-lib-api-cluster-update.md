# `update_cluster`<a name="pc-py-lib-api-cluster-update"></a>

```
update_cluster(cluster_name, cluster_configuration, suppress_validators, validation_failure_level, region, force_update, dry_run, wait)
```

Update a cluster in a given Region\.Parameters:

**`cluster_name` \(required\)**  
The cluster name\.

**`cluster_configuration` \(required\)**  
The cluster configuration as a Python data type\.

**`suppress_validators`**  
Identifies one or more cluster configuration validators to suppress\.  
Format: `(ALL | type:[A-Za-z0-9]+)`

**`validation_failure_level`**  
The minimum validation level that causes the cluster update to fail\. The default is `ERROR`\.  
Valid values: `INFO` \| `WARNING` \| `ERROR`

**`region`**  
The cluster AWS Region\.

**`dry_run`**  
Performs the request validation without creating or updating any resources\. You can use this to validate the cluster configuration\. The default is `False`\.

**`force_update`**  
If set to `True`, forces the update by ignoring the update validation errors\. The default is `False`\.

**`wait`**  
If set to `True`, waits for the operation to complete\. The default is `False`\.