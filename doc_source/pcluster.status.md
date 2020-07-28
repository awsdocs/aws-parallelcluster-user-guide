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
Shows the help text for the specified command\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

`-nw, --nowait`  
Indicates not to wait for stack events after executing a stack command\.  
Defaults to `False`\.