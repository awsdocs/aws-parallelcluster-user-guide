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
Shows the help text for the specified command\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

This command sets the Auto Scaling Group parameters to one of the following: 
+ The initial configuration values \(`max_queue_size` and `initial_queue_size`\) from the template that was used to create the cluster\.
+ The configuration values that were used to update the cluster since it was first created\.