# `get_cluster_log_events`<a name="pc-py-lib-api-logs-cluster-stack-log-events"></a>

```
get_cluster_log_events(cluster_name, log_stream_name, region, next_token, start_from_head, limit, start_time, end_time)
```

Get log events for a given cluster and log stream\.Parameters:

**`cluster_name` \(required\)**  
The cluster name\.

**`log_stream_name` \(required\)**  
The log stream name\.

**`region`**  
The cluster AWS Region\.

**`next_token`**  
The token to use for paginated requests\.

**`start_from_head`**  
If set to `True`, AWS ParallelCluster returns the earliest log events first\. If set to `False`, it returns the latest log events first\. The default is `False`\.

**`limit`**  
The maximum number of log events returned\. If you don't specify a value, the maximum is the number of logs that can fit in a response size of 1 MB, up to 10,000 log events\.

**`start_time`**  
The start of the time range for log events, expressed in ISO 8601 format; for example, `'2021-01-01T20:00:00Z'`\. Events with a timestamp equal to, or later than, this time are included\.

**`end_time`**  
The end of the time range for log events, expressed in ISO 8601 format; for example, `'2021-01-01T20:00:00Z'`\. Events with a timestamp equal to, or later than, this time are not included\.