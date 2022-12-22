# `pcluster update`<a name="pcluster.update"></a>

Analyzes the configuration file to determine if the cluster can be safely updated\. If the analysis determines the cluster can be updated, you are prompted to confirm the change\. If the analysis shows the cluster can't be updated, the configuration settings that are the source of the conflicts are enumerated with details\. For more information, see [Using `pcluster update`](using-pcluster-update.md)\.

```
pcluster update [ -h ] [ -c CONFIG_FILE ] [ --force ] [ -r REGION ] [ -nr ]
                [ -nw ] [ -t CLUSTER_TEMPLATE ] [ -p EXTRA_PARAMETERS ] [ -rd ]
                [ --yes ] cluster_name
```

## Positional arguments<a name="pcluster.update.arg"></a>

`cluster_name`  
Specifies the name of the cluster to update\.

## Named arguments<a name="pcluster.update.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster update`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`--force`  
Enables an update even if one or more settings has a blocking change or if an outstanding action is required \(such as stopping the compute fleet\) before the update can proceed\. This shouldn't be combined with the `--yes` argument\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the AWS Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

`-nr, --norollback`  
Disables AWS CloudFormation stack rollback on error\.  
Defaults to `False`\.

`-nw, --nowait`  
Indicates not to wait for stack events after processing a stack command\.  
Defaults to `False`\.

`-t CLUSTER_TEMPLATE, --cluster-template CLUSTER_TEMPLATE`  
Specifies the section of the cluster template to use\.

`-p EXTRA_PARAMETERS, --extra-parameters EXTRA_PARAMETERS`  
Adds extra parameters to a stack update\.

`-rd, --reset-desired`  
Resets the current capacity of an Auto Scaling Group to the initial configuration values\.  
Defaults to `False`\.

`--yes`  
Automatically assumes that the answer to all prompts is yes\. This should not be combined with the `--force` argument\.

```
$ pcluster update -c path/to/config mycluster
Retrieving configuration from CloudFormation for cluster mycluster...
Validating configuration file .parallelcluster/config...
Found Configuration Changes:

#    parameter                   old value    new value
---  --------------------------  -----------  -----------
     [compute_resource default]
01   min_count                   1            2
02   max_count                   5            12

Validating configuration update...
Congratulations! The new configuration can be safely applied to your cluster.
Do you want to proceed with the update? - Y/N: Y
Updating: mycluster
Calling update_stack
Status: parallelcluster-mycluster - UPDATE_COMPLETE
```

When the command is called and begins polling for the status of that call, it's safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.