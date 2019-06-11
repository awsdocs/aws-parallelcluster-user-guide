# `awsbqueues`<a name="awsbatchcli_awsbqueues"></a>

Shows the job queue that is associated with the cluster\.

```
awsbqueues [ - h ] [ - c CLUSTER ] [ - d ] [ job_queues [ job_queues ... ]]
```

## Positional Arguments<a name="pcluster.awsbqueues.arguments"></a>

`job_queues`  
Specifies the space\-separated list of queue names to show\. If a single queue is requested, it is shown in a detailed version\.

## Named Arguments<a name="pcluster.awsbqueues.namedarguments"></a>

`-c CLUSTER, --cluster CLUSTER`  
Specifies the name of the cluster to use\.

`-d, --details`  
Indicates whether to show the details of the queues\.  
Default: False