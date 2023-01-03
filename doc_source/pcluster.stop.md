# `pcluster stop`<a name="pcluster.stop"></a>

Stops the compute fleet, leaving the head node running\.

```
pcluster stop [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] cluster_name
```

## Positional arguments<a name="pcluster.stop.posarg"></a>

`cluster_name`  
Stops the compute fleet of the provided cluster name\.

**Example using AWS ParallelCluster version 2\.11\.7:**

## Named arguments<a name="pcluster.stop.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster stop`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the AWS Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

```
$ pcluster stop mycluster
Compute fleet status is: STOPPED. Submitting status change request.
Request submitted successfully. It might take a while for the transition to complete.
Please run 'pcluster status' if you need to check compute fleet status
```

Sets the Auto Scaling group parameters to min/max/desired = 0/0/0, and terminates the compute fleet\. The head remains running\. To terminate all EC2 resources and avoid EC2 charges, consider deleting the cluster\.