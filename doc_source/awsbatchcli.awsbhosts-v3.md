# `awsbhosts`<a name="awsbatchcli.awsbhosts-v3"></a>

Shows the hosts that belong to the cluster’s compute environment\.

```
awsbhosts [-h] [-c CLUSTER] [-d] [instance_ids [instance_ids ... ]]
```

## Positional Arguments<a name="awsbatchcli.awsbhosts-v3.arguments"></a>

`instance_ids`  
Specifies a space\-separated list of instances IDs\. If a single instance is requested, it is shown in a detailed version\.

## Named Arguments<a name="awsbatchcli.awsbhosts-v3.namedarguments"></a>

`-c CLUSTER, --cluster CLUSTER`  
Specifies the name of the cluster to use\.

`-d, --details`  
Indicates whether to show the details of the hosts\.  
Default: False