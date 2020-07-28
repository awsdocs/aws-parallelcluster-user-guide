# `pcluster list`<a name="pcluster.list"></a>

Displays a list of stacks that are associated with AWS ParallelCluster\.

```
pcluster list [ -h ] [ -c CONFIG_FILE ] [ -r REGION ]
```

## Named arguments<a name="pcluster.list.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`--color`  
Displays the cluster status in color\.  
Defaults to `False`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `c`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.

Lists the name of any AWS CloudFormation stacks named `parallelcluster-*`\.