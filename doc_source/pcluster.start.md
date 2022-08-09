# `pcluster start`<a name="pcluster.start"></a>

Starts the compute fleet for a cluster that has been stopped\.

```
pcluster start [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] cluster_name
```

## Positional arguments<a name="pcluster.start.arg"></a>

`cluster_name`  
Starts the compute fleet of the provided cluster name\.

## Named arguments<a name="pcluster.start.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster start`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

**Example using AWS ParallelCluster version 2\.11\.7:**

```
$ pcluster start mycluster
Compute fleet status is: RUNNING. Submitting status change request.
Request submitted successfully. It might take a while for the transition to complete.
Please run 'pcluster status' if you need to check compute fleet status
```

This command sets the Auto Scaling Group parameters to one of the following: 
+ The initial configuration values \(`max_queue_size` and `initial_queue_size`\) from the template that was used to create the cluster\.
+ The configuration values that were used to update the cluster since it was first created\.