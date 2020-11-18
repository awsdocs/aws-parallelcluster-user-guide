# `pcluster status`<a name="pcluster.status"></a>

Pulls the current status of the cluster\.

```
pcluster status [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] [ -nw ] cluster_name
```

## Positional arguments<a name="pcluster.status.posarg"></a>

`cluster_name`  
Shows the status of the cluster with the provided name\.

## Named arguments<a name="pcluster.status.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster status`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

`-nw, --nowait`  
Indicates not to wait for stack events after processing a stack command\.  
Defaults to `False`\.