# `pcluster list-cluster-log-streams`<a name="pcluster.list-cluster-log-streams-v3"></a>

Retrieve the list of log streams associated with a cluster\.

```
pcluster list-cluster-log-streams [-h]
                 --cluster-name CLUSTER_NAME                              
                [--filters FILTERS [FILTERS ...]]
                [--next-token NEXT_TOKEN] [--debug]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.list-cluster-log-streams.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-cluster-log-streams`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--debug`  
Enables debug logging\.

`--filters FILTERS [FILTERS ...]`  
Specifies filters for the log streams\. Format: `Name=a,Values=1 Name=b,Values=2,3`\. Supported filters are:    
`private-dns-name`  
Specifies the short form of the private DNS name of the instance \(e\.g\. `ip-10-0-0-101`\)\.  
`node-type`  
Specifies the node type, the only accepted value for this filter is `HeadNode`\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster list-cluster-log-streams \
    -n cluster-v3 \
    -r us-east-1 \
    --query 'logStreams[*].logStreamName'
[
  "ip-172-31-58-205.i-1234567890abcdef0.cfn-init",
  "ip-172-31-58-205.i-1234567890abcdef0.chef-client",
  "ip-172-31-58-205.i-1234567890abcdef0.cloud-init",
  "ip-172-31-58-205.i-1234567890abcdef0.clustermgtd",
  "ip-172-31-58-205.i-1234567890abcdef0.slurmctld",
  "ip-172-31-58-205.i-1234567890abcdef0.supervisord",
  "ip-172-31-58-205.i-1234567890abcdef0.system-messages"
]
```