# `awsbqueues`<a name="awsbatchcli.awsbqueues-v3"></a>

Shows the job queue that is associated with the cluster\.

```
awsbqueues [-h] [-c CLUSTER] [-d] [job_queues [job_queues ... ]]
```

## Positional arguments<a name="awsbatchcli.awsbqueues-v3.arguments"></a>

`job_queues`  
Specifies the space\-separated list of queue names to show\. If a single queue is requested, it is shown in a detailed version\.

## Named arguments<a name="awsbatchcli.awsbqueues-v3.namedarguments"></a>

`-c CLUSTER, --cluster CLUSTER`  
Specifies the name of the cluster to use\.

`-d, --details`  
Indicates whether to show the details of the queues\.  
Default: False