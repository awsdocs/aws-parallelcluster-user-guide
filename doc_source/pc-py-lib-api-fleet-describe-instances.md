# `describe_cluster_instances`<a name="pc-py-lib-api-fleet-describe-instances"></a>

```
describe_cluster_instances(cluster_name, region, next_token, node_type, queue_name)
```

Describe a cluster's instances\.Parameters:

**`cluster_name` \(required\)**  
The cluster name\.

**`region`**  
The cluster AWS Region\.

**`next_token`**  
The token to use for paginated requests\.

**`node_type`**  
Filters the instances by `node_type`\.  
Valid values: `HeadNode` \| `ComputeNode`

**`queue_name`**  
Filters the instances by queue name\.