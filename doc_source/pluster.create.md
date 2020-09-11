# `pcluster create`<a name="pluster.create"></a>

Creates a new cluster\.

```
pcluster create [ -h ] [ -c CONFIG_FILE ] [ -r REGION ] [ -nw ] [ -nr ]
                [ -u TEMPLATE_URL ] [ -t CLUSTER_TEMPLATE ]
                [ -p EXTRA_PARAMETERS ] [ -g TAGS ]
                cluster_name
```

## Positional arguments<a name="pluster.create.arg"></a>

`cluster_name`  
Defines the name of the cluster\. The AWS CloudFormation stack name is `parallelcluster-cluster_name`\.

## Named arguments<a name="pluster.create.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster create`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

`-nw, --nowait`  
Indicates not to wait for stack events after executing a stack command\.  
Defaults to `False`\.

`-nr, --norollback`  
Disables stack rollback on error\.  
Defaults to `False`\.

`-u TEMPLATE_URL, --template-url TEMPLATE_URL`  
Specifies a URL for the custom AWS CloudFormation template, if it was used at creation time\.

`-t CLUSTER_TEMPLATE, --cluster-template CLUSTER_TEMPLATE`  
Indicates the cluster template to use\.

`-p EXTRA_PARAMETERS, --extra-parameters EXTRA_PARAMETERS`  
Adds extra parameters to stack create\.

`-g TAGS, --tags TAGS`  
Specifies additional tags to add to the stack\.

When the command is called and begins polling for the status of that call, it is safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.

Examples:

```
$ pcluster create mycluster
$ pcluster create mycluster --tags '{ "Key1" : "Value1" , "Key2" : "Value2" }'
```