# `pcluster list`<a name="pcluster.list"></a>

Displays a list of stacks that are associated with AWS ParallelCluster\.

```
pcluster list [ -h ] [ -c CONFIG_FILE ] [ -r REGION ]
```

## Named arguments<a name="pcluster.list.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster list`\.

`--color`  
Displays the cluster status in color\.  
Defaults to `False`\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `c`\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

Lists the name of any AWS CloudFormation stacks named `parallelcluster-*`\.

**Example using AWS ParallelCluster version 2\.11\.7:**

```
$ pcluster list -c path/to/config -r us-east-1
mycluster            CREATE_IN_PROGRESS  2.11.7
myothercluster       CREATE_IN_PROGRESS  2.11.7
```