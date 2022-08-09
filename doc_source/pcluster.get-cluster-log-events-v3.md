# `pcluster get-cluster-log-events`<a name="pcluster.get-cluster-log-events-v3"></a>

Retrieve the events associated with a log stream\.

```
pcluster get-cluster-log-events [-h] 
                 --cluster-name CLUSTER_NAME
                 --log-stream-name LOG_STREAM_NAME
                [--debug]
                [--end-time END_TIME]
                [--limit LIMIT]
                [--next-token NEXT_TOKEN]
                [--query QUERY]
                [--region REGION]
                [--start-from-head START_FROM_HEAD]                                
                [--start-time START_TIME]
```

## Named arguments<a name="pcluster-v3.get-cluster-log-events.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster get-cluster-log-events`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--log-stream-name LOG_STREAM_NAME`  
Specifies the name of the log stream\. You can use the `list-cluster-log-streams` command to retrieve a log stream associated with an event or events\.

`--debug`  
Enables debug logging\.

`--end-time END_TIME`  
Specifies the end of the time range, expressed in ISO 8601 format \(`YYYY-MM-DDThh:mm:ssZ`, for example `2021-01-01T20:00:00Z`\)\. Events with a timestamp equal to or later than this time are not included\.

`--limit LIMIT`  
Specifies the maximum number of log events returned\. If a value is not specified, the maximum is as many log events as can fit in a response size of 1 MB, up to 10,000 log events\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--start-from-head START_FROM_HEAD`  
If the value is `true`, the earliest log events are returned first\. If the value is `false`, the most recent log events are returned first\. \(Defaults to `false`\.\)

`--start-time START_TIME`  
Specifies the start of the time range, expressed in ISO 8601 format \(`YYYY-MM-DDThh:mm:ssZ`, for example `2021-01-01T20:00:00Z`\)\. Events with a timestamp equal to this time or later than this time are included\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster get-cluster-log-events \
    -c cluster-v3 \
    -r us-east-1 \
    --log-stream-name ip-198-51-100-44.i-1234567890abcdef0.clustermgtd \
    --limit 3
{
  "nextToken": "f/36966906399261933213029082268132291405859205452101451780/s",
  "prevToken": "b/36966906399239632467830551644990755687586557090595471362/s",
  "events": [
    {
      "message": "2022-07-12 19:16:53,379 - [slurm_plugin.clustermgtd:_maintain_nodes] - INFO - Performing node maintenance actions",
      "timestamp": "2022-07-12T19:16:53.379Z"
    },
    {
      "message": "2022-07-12 19:16:53,380 - [slurm_plugin.clustermgtd:_maintain_nodes] - INFO - Following nodes are currently in replacement: (x0) []",
      "timestamp": "2022-07-12T19:16:53.380Z"
    },
    {
      "message": "2022-07-12 19:16:53,380 - [slurm_plugin.clustermgtd:_terminate_orphaned_instances] - INFO - Checking for orphaned instance",
      "timestamp": "2022-07-12T19:16:53.380Z"
    }
  ]
}
```