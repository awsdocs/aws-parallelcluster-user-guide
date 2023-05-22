# `update_compute_fleet`<a name="pc-py-lib-api-fleet-update"></a>

```
update_compute_fleet(cluster_name, status, region)
```

Update the status of the cluster compute fleet\.Parameters:

**`cluster_name` \(required\)**  
The cluster name\.

**`status` \(required\)**  
The status to update to\.  
Valid values: `START_REQUESTED` \| `STOP_REQUESTED` \| `ENABLED` \| `DISABLED`

**`region`**  
The cluster AWS Region\.