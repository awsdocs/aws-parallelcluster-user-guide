# `pcluster update`<a name="pcluster.update"></a>

Updates a running cluster by using the values in the configuration file\.

```
pcluster update [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] [ -nw ] [ -nr ]
                [ -t CLUSTER_TEMPLATE ] [ -p EXTRA_PARAMETERS ] [ -rd ]
                cluster_name
```

## Positional Arguments<a name="pcluster.update.arg"></a>

`cluster_name`  
Specifies the name of the cluster to update\.

## Named Arguments<a name="pcluster.update.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the full path of the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

`-nw, --nowait`  
Indicates not to wait for stack events after executing a stack command\.  
Defaults to `False`\.

`-nr, --norollback`  
Disables AWS CloudFormation stack rollback on error\.  
Defaults to `False`\.

`-t CLUSTER_TEMPLATE, --cluster-template CLUSTER_TEMPLATE`  
Specifies the section of the cluster template to use\.

`-p EXTRA_PARAMETERS, --extra-parameters EXTRA_PARAMETERS`  
Adds extra parameters to a stack update\.

`-rd, --reset-desired`  
Resets the current capacity of an Auto Scaling Group to the initial configuration values\.  
Defaults to `False`\.

When the command is called and begins polling for the status of that call, it is safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.