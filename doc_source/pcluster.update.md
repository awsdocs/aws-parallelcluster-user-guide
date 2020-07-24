# `pcluster update`<a name="pcluster.update"></a>

Analyzes the configuration file to determine if the cluster can be safely updated\. If the analysis determines the cluster can be updated, the user is prompted to confirm the change\. If the analysis shows the cluster cannot be updated, the configuration settings that are the source of the conflicts are enumerated with details\. For more information, see [Using `pcluster update`](using-pcluster-update.md)\.

```
pcluster update [ -h ] [ -c CONFIG_FILE ] [ --force ] [ -r REGION ] [ -nr ]
                [ -nw ] [ -t CLUSTER_TEMPLATE ] [ -p EXTRA_PARAMETERS ] [ -rd ]
                [ --yes ] cluster_name
```

## Positional Arguments<a name="pcluster.update.arg"></a>

`cluster_name`  
Specifies the name of the cluster to update\.

## Named Arguments<a name="pcluster.update.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`--force`  
Enables an update even if one or more settings has a blocking change, or that an outstanding action is required \(such as stopping the compute fleet\) before the update can proceed\. This should not be combined with the `--yes` argument\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

`-nr, --norollback`  
Disables AWS CloudFormation stack rollback on error\.  
Defaults to `False`\.

`-nw, --nowait`  
Indicates not to wait for stack events after executing a stack command\.  
Defaults to `False`\.

`-t CLUSTER_TEMPLATE, --cluster-template CLUSTER_TEMPLATE`  
Specifies the section of the cluster template to use\.

`-p EXTRA_PARAMETERS, --extra-parameters EXTRA_PARAMETERS`  
Adds extra parameters to a stack update\.

`-rd, --reset-desired`  
Resets the current capacity of an Auto Scaling Group to the initial configuration values\.  
Defaults to `False`\.

`--yes`  
Automatically assumes the answer to all prompts is yes\. This should not be combined with the `--force` argument\.

When the command is called and begins polling for the status of that call, it is safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.