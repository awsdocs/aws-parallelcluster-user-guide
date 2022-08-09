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
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

**Example using AWS ParallelCluster version 2\.11\.7:**

```
$ pcluster instances -c path/to/config -r us-east-1 mycluster
MasterServer         i-1234567890abcdef0
ComputeFleet         i-abcdef01234567890
```