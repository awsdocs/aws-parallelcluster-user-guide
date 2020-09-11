# `pcluster instances`<a name="pcluster.instances"></a>

Displays a list of all instances in a cluster\.

```
pcluster instances [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] cluster_name
```

## Positional arguments<a name="pcluster.instances.arg"></a>

***cluster\_name***  
Displays the instances for the cluster with the provided name\.

## Named arguments<a name="pcluster.instances.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster instances`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.