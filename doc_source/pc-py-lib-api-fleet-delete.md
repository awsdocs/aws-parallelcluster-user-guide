# `delete_cluster_instances`<a name="pc-py-lib-api-fleet-delete"></a>

```
delete_cluster_instances(cluster_name, region, force)
```

Delete a cluster in a given Region\.Parameters:

**`cluster_name` \(required\)**  
The cluster name\.

**`region`**  
The cluster AWS Region\.

**`force`**  
If set to `True`, forces deletion when the cluster with the given `cluster_name` isn't found\. The default is `False`\.