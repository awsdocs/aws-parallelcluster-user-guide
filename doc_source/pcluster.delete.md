# `pcluster delete`<a name="pcluster.delete"></a>

Deletes a cluster\.

```
pcluster delete [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] [ -nw ] cluster_name
```

## Positional Arguments<a name="pcluster.delete.arg"></a>

`cluster_name`  
Specifies the name of the cluster to delete\.

## Named Arguments<a name="pcluster.delete.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the full path of the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

When the command is called and begins polling for the status of that call, it is safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.